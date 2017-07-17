---
layout: post
title: Converting JSON to a hashtable
excerpt: Extending the ConvertFrom-Json cmdlet with a wrapper
tags: [posh]
---

# Table of Contents

* TOC
{:toc}

# What's the problem?

The ConvertFrom-Json and ConvertTo-Json cmdlets are great. JSON is a pretty common standard that isn't going anywhere, and the ability to work with JSON data as native PowerShell objects provides first-class support for this format in PowerShell. It's far simpler for PS to use JSON than to use XML.

However, there are times when the PSCustomObject returned by ConvertFrom-Json is less than ideal, and it would be more helpful to use a hashtable instead:

## Strict mode and missing properties

When using Strict mode, checking whether the JSON contained a key uses this fairly tedious snippet:

{% highlight powershell}
    $obj = $json | ConvertFrom-Json
    if ($obj.PSObject.Properties.Name -contains 'MyCustomKey') {
        # ...
    }
{% endhighlight }

Hashtables make this far easier:

{% highlight powershell}
    $hash = @{ a = 1; b = 2;}
    if ($hash.ContainsKey('MyCustomKey')) {
        # ...
    }

    # Also valid
    if ($hash['MyCustomKey']) {
        # ...
    }
{% endhighlight }

## Extending and combining objects

PSCustomObjects have to be extended using Add-Member:

{% highlight powershell}
    $obj | Add-Member -MemberType NoteProperty -Name 'MyCustomKey' -Value 'foo'
{% endhighlight }

Hashtables can have additional properties added implicitly, even if the key doesn't exist:

{% highlight powershell}
    $hash['MyCustomKey'] = 'foo'

    # More formal
    $hash.Add('MyCustomKey', 'foo')
{% endhighlight }

Hashtables can also be combined easily:

{% highlight powershell}
    $hash1 = @{a = 1}
    $hash2 = @{b = 2}

    $hash3 = $hash1 + $hash2
{% endhighlight }

Is there a convenient way for us to create a hashtable from JSON?

# That's a Wrap

Converting JSON to a hashtable is actually fairly simple with the lesser-known [JavaScriptSerializer class](https://msdn.microsoft.com/en-us/library/system.web.script.serialization.javascriptserializer(v=vs.110).aspx). (I believe this was added in .NET 4.5, but if I'm incorrect, feel free to let me know.) This assembly isn't loaded by default in a PowerShell session, but thanks to `Add-Type`, loading an assembly is trivial (notice that the MSDN page tells us exactly what DLL file to reference).

This is only half of the problem, though. We could write a function to wrap this class, but then any code where we need to reference our JSON-to-hashtable logic would need to include or reference that function. We'd need to do a find/replace to change all references of `ConvertTo-Json` to our custom function name.

Instead, PowerShell's command precedence allows us to define a custom function that "overrides" a built-in cmdlet. PowerShell resolves commands in this order:

1. Aliases
2. Functions
3. Native cmdlets
4. Binaries (anything in the PATH variable)

Because of this, we can create a custom function and name it `ConvertTo-Json`, and it will magically be used by default anywhere we call `ConvertTo-Json` in that PowerShell session.

You can create a wrapper function yourself line-by-line if you'd like, but I found Jeff Hicks' [Copy-Command function](https://www.petri.com/making-powershell-command) gave me a pretty good starting point:

{% highlight powershell}
    PS> Copy-Command -Command 'ConvertFrom-Json' -UseForwardHelp -IncludeDynamic | Out-File C:\PowerShell\Scripts\ConvertFrom-Json.ps1 -Force
{% endhighlight }

# The Code

Here's the full code for my custom wrapper function around `ConvertTo-Json`:

{% highlight powershell}
    function ConvertFrom-Json {
        <#
        .ForwardHelpTargetName Microsoft.PowerShell.Utility\ConvertFrom-Json
        .ForwardHelpCategory Cmdlet
        #>
        [CmdletBinding(HelpUri = 'http://go.microsoft.com/fwlink/?LinkID=217031', RemotingCapability = 'None')]
        param(
            [Parameter(Mandatory = $true,
                Position = 0,
                ValueFromPipeline = $true)]
            [AllowEmptyString()]
            [String] $InputObject,

            [Parameter()]
            [ValidateSet('Object', 'Hashtable')]
            [String] $As = 'Object'
        )

        begin {
            Write-Debug "Beginning $($MyInvocation.Mycommand)"
            Write-Debug "Bound parameters:`n$($PSBoundParameters | out-string)"

            try {
                # Use this class to perform the deserialization:
                # https://msdn.microsoft.com/en-us/library/system.web.script.serialization.javascriptserializer(v=vs.110).aspx
                Add-Type -AssemblyName "System.Web.Extensions, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" -ErrorAction Stop
            }
            catch {
                throw "Unable to locate the System.Web.Extensions namespace from System.Web.Extensions.dll. Are you using .NET 4.5 or greater?"
            }

            $jsSerializer = New-Object -TypeName System.Web.Script.Serialization.JavaScriptSerializer
        }

        process {
            switch ($As) {
                'Hashtable' {
                    $jsSerializer.Deserialize($InputObject, 'Hashtable')
                }
                default {
                    # If we don't know what to do, use the native cmdlet.
                    # This should also catch the -As Object case.

                    # Remove -As since the native cmdlet doesn't understand it
                    if ($PSBoundParameters.ContainsKey('As')) {
                        $PSBoundParameters.Remove('As')
                    }

                    Write-Debug "Running native ConvertFrom-Json with parameters:`n$($PSBoundParameters | Out-String)"
                    Microsoft.PowerShell.Utility\ConvertFrom-Json @PSBoundParameters
                }
            }
        }

        end {
            $jsSerializer = $null
            Write-Debug "Completed $($MyInvocation.Mycommand)"
        }

    }

{% endhighlight }

# Example Usage

{% highlight powershell}
    PS> $json = @'
    {
        "cat":  "meow",
        "dog":  "woof",
        "programmer":  "derp"
    }
    '@

    PS> $json | ConvertFrom-Json

    cat  dog  programmer
    ---  ---  ----------
    meow woof derp

    PS> PS> $json | ConvertFrom-Json -As Hashtable

    Name                           Value
    ----                           -----
    dog                            woof
    programmer                     derp
    cat                            meow

{% endhighlight }

Notice that the default behavior doesn't change - if we don't specify what we want to do with the JSON, it will be converted to an object as usual. This prevents any disruption to code that uses the built-in cmdlet and expects an object in response.

# Conclusion

It be worthwhile to wrap the [JSON.NET library](http://www.newtonsoft.com/json) rather than the .NET class I referenced, since even MSDN has a note that serialization should be done with JSON.NET rather than that class. That said, since that would introduce a dependent library, it would be a candidate for a more full-featured module instead of a quick wrapper function.

In fact, [PowerShell Core seems to be referencing this library already!](https://github.com/PowerShell/PowerShell/blob/7aa7f3858cf41e4474b07fd4ed4b0b2f25fdb831/src/Microsoft.PowerShell.Commands.Utility/commands/utility/WebCmdlet/ConvertFromJsonCommand.cs#L44)

Finally, if you plan to use this wrapper in code that gets pushed anywhere other than your local machine, it would be prudent to include the wrapper function as a private function in your module. That way, you don't have to assume that the other system has this function available in its PowerShell profile.

I hope this is helpful!

# References

* [Everything you wanted to know about hashtables](https://kevinmarquette.github.io/2016-11-06-powershell-hashtable-everything-you-wanted-to-know-about/#converting-json-to-hashtable) (Kevin Marquette)
* [Making a PowerShell Command Your Own](https://www.petri.com/making-powershell-command) (Jeff Hicks)
