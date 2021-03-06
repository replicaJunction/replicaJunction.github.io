---
layout: post
title: Suppressing PSScriptAnalyzer in Pester
excerpt: Sometimes your tests need to take shortcuts
tags: [posh]
---

# Table of Contents

* TOC
{:toc}

# PSScriptAnalyzer is great...but...

I've recently made some changes to the continuous integration setup for the [PSJira](https://github.com/replicaJunction/PSJira) module. Previously, I'd been coding against the *dev* branch and only deploying to the PowerShell Gallery when I merged changes back to *master*. While that's probably a good practice for a "real" development workflow, since PSJira is a small project and I am the primary developer on it (though there have been some great contributions by the community), I decided to adopt a more streamlined approach. I'm now coding features in their own branches, and publishing to the Gallery any time I merge a branch back to *master*.

Shortly after making these changes, I received the following e-mail from Microsoft:

    Hi replicaJunction,

    Thank you for contributing to the PowerShell Gallery. We have analyzed your item, PSJira 1.2.5.113 and found it contains the following issues:

    RuleName                                        Severity FileName                      Line  Message
    PSAvoidUsingConvertToSecureStringWithPlainText  Error    Invoke-JiraMethod.Tests.ps1   47    File 'Invoke-JiraMethod.Tests.ps1' uses ConvertTo-SecureString with plaintext. This will expose secure information. Encrypted standard strings should be used instead.
    PSAvoidUsingConvertToSecureStringWithPlainText  Error    New-JiraSession.Tests.ps1     14    File 'New-JiraSession.Tests.ps1' uses ConvertTo-SecureString with plaintext. This will expose secure information. Encrypted standard strings should be used instead.
    PSAvoidUsingConvertToSecureStringWithPlainText  Error    Remove-JiraSession.Tests.ps1  14    File 'Remove-JiraSession.Tests.ps1' uses ConvertTo-SecureString with plaintext. This will expose secure information. Encrypted standard strings should be used instead.

    These errors were generated by PowerShell Script Analyzer, which can be found at http://www.powershellgallery.com/packages/PSScriptAnalyzer/.
    Please resolve the issues and then republish your item to the gallery.

    Your feedback about PowerShell Script Analyzer rules or the scan itself is welcome. If you feel that the rules applied in your item's scan results are too restrictive or can result in false hits, please file an issue at https://github.com/powershell/psscriptanalyzer/issues. Alternatively, you can respond to this thread and let us know.

    If you would like to unlist your item until the issue is resolved, please follow these steps:

    1. Open http://www.powershellgallery.com, and sign in with your account credentials.
    2. Click your account name in the upper right of the page.
    3. Click Manage My Items.
    4. Next to the item you want to unlist, click the Unlist icon.

    If you would like us to delete the original item version, respond and let us know. Otherwise, your item can remain unlisted.

    Thank you for your cooperation,
    PowerShell Gallery Administrators

I've committed to *master* several times in the last couple of weeks, and that e-mail was dutifully resent once for each version of the module that got published.

It's very cool that Microsoft is running the PSScriptAnalyzer against stuff submitted to the gallery. I noticed right away, though, that all three hits were from Pester tests, and all three had the same complaint about ```ConvertTo-SecureString -AsPlainText -Force```. I opened up the files to double-check what I was doing, and they were excerpts like this:

{% highlight powershell %}
$testUri = 'http://example.com'
$testUsername = 'testUsername'
$testPassword = 'password123'
$testCred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $testUsername,(ConvertTo-SecureString -AsPlainText -Force $testPassword)
{% endhighlight %}

Clearly this would be a bad idea in production code, and I can see why PSScriptAnalyzer flagged it. It's just as clear, though, that this is a test case - no real user account exists at example.com with those credentials. (At least, it shouldn't!)

Could I refactor the code to avoid using the PSCredential object? Probably. A full review and rewrite of the tests for PSJira would probably be worth my time. For now, though, I've reviewed the code in question and confirmed that it's not causing any harm. I'd like to dismiss the warning, or tell PSScriptAnalyzer that it can ignore that specific message in this specific context.

# Reproducing the Problem

The first step in this process is getting my local environment set up to replicate the tests Microsoft runs. That saves me from waiting on e-mails from Microsoft just to tell me that my code isn't fixed.

{% highlight powershell %}
PS> Find-Module PSScriptAnalyzer

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
1.7.0      PSScriptAnalyzer                    PSGallery            PSScriptAnalyzer provides script analysis and checks for potential code defects in the scripts by applying a group of built-in or customized rules on the script...

PS> Install-Module PSScriptAnalyzer

PS> Import-Module PSScriptAnalyzer

PS> gcm -Module PSScriptAnalyzer

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-ScriptAnalyzerRule                             1.7.0      PSScriptAnalyzer
Cmdlet          Invoke-ScriptAnalyzer                              1.7.0      PSScriptAnalyzer

PS> Invoke-ScriptAnalyzer

cmdlet Invoke-ScriptAnalyzer at command pipeline position 1
Supply values for the following parameters:
Path:
{% endhighlight %}

Ah, apparently the -Path parameter is mandatory. I can't just run it from the current directory, like running ```Invoke-Pester``` or similar.

{% highlight powershell %}
PS> Invoke-ScriptAnalyzer -Path D:\Documents\Projects\PSJira
{% endhighlight %}

At this point, PowerShell began to think, and churn, and slowly spit out...a lot more issues than that e-mail had pointed out. Here's a small excerpt:

{% highlight powershell %}
RuleName                            Severity     FileName   Line  Message
--------                            --------     --------   ----  -------
PSUsePSCredentialType               Warning      Invoke-Jir 1     The Credential parameter in 'Invoke-JiraMethod' must be of
                                                 aMethod.ps       type PSCredential. For PowerShell 4.0 and earlier, please
                                                 1                define a credential transformation attribute, e.g.
                                                                  [System.Management.Automation.Credential()], after the
                                                                  PSCredential type attribute.
PSPossibleIncorrectComparisonWithNu Warning      Invoke-Jir 108   $null should be on the left side of equality comparisons.
ll                                               aMethod.ps
                                                 1
PSAvoidUsingConvertToSecureStringWi Error        Invoke-Jir 59    File 'Invoke-JiraMethod.Tests.ps1' uses
thPlainText                                      aMethod.Te       ConvertTo-SecureString with plaintext. This will expose
                                                 sts.ps1          secure information. Encrypted standard strings should be
                                                                  used instead.
PSAvoidUsingWriteHost               Warning      Invoke-Jir 19    File 'Invoke-JiraMethod.Tests.ps1' uses Write-Host. Avoid
                                                 aMethod.Te       using Write-Host because it might not work in all hosts,
                                                 sts.ps1          does not work when there is no host, and (prior to PS 5.0)
                                                                  cannot be suppressed, captured, or redirected. Instead, use
                                                                  Write-Output, Write-Verbose, or Write-Information.
PSAvoidUsingCmdletAliases           Warning      Invoke-Jir 49    '?' is an alias of 'Where-Object'. Alias can introduce
                                                 aMethod.Te       possible problems and make scripts hard to maintain. Please
                                                 sts.ps1          consider changing alias to its full content.
PSAvoidUsingCmdletAliases           Warning      Invoke-Jir 462   '?' is an alias of 'Where-Object'. Alias can introduce
                                                 aMethod.Te       possible problems and make scripts hard to maintain. Please
                                                 sts.ps1          consider changing alias to its full content.
PSUseDeclaredVarsMoreThanAssignment Warning      Add-JiraGr 121   The variable 'result' is assigned but never used.
s                                                oupMember.
                                                 ps1
{% endhighlight %}

Notice one of the credential issues appears here. Looks like Microsoft only contacted me about errors...I was left to find out about the warnings all by myself. What a lovely surprise!

Joking aside, I looked over the warnings. There were a ton of warnings about Write-Host (and for good reason, since [every time you use Write-Host, puppies' lives are in danger](http://community.idera.com/powershell/powershell_com_featured_blogs/b/donjones/posts/2012-scripting-games-commentary)). This is a design approach I use in some Pester tests to provide some extra debug feedback - none of the Write-Host warnings appeared in anything but a Pester test. On the other hand, some of the warnings are things I should address - for example, there are several cases where I've declared variables, then never used them.

Still, at the moment, I'm only interested in the issues Microsoft sent me. I re-ran the PSScriptAnalyzer command and filtered for only errors, and the results came back much faster:

{% highlight powershell %}
PS> Invoke-ScriptAnalyzer -Path D:\Documents\Projects\PSJira -Recurse -Severity Error

RuleName                            Severity     FileName   Line  Message
--------                            --------     --------   ----  -------
PSAvoidUsingConvertToSecureStringWi Error        Invoke-Jir 59    File 'Invoke-JiraMethod.Tests.ps1' uses
thPlainText                                      aMethod.Te       ConvertTo-SecureString with plaintext. This will expose
                                                 sts.ps1          secure information. Encrypted standard strings should be
                                                                  used instead.
PSAvoidUsingConvertToSecureStringWi Error        New-JiraSe 14    File 'New-JiraSession.Tests.ps1' uses
thPlainText                                      ssion.Test       ConvertTo-SecureString with plaintext. This will expose
                                                 s.ps1            secure information. Encrypted standard strings should be
                                                                  used instead.
PSAvoidUsingConvertToSecureStringWi Error        Remove-Jir 14    File 'Remove-JiraSession.Tests.ps1' uses
thPlainText                                      aSession.T       ConvertTo-SecureString with plaintext. This will expose
                                                 ests.ps1         secure information. Encrypted standard strings should be
                                                                  used instead.
{% endhighlight %}

Bingo!

Now that I know the command that can reproduce the issue, I can set about addressing it.

# Looking the Other Way

According to the [PSScriptAnalyzer readme file](https://github.com/PowerShell/PSScriptAnalyzer/blob/development/README.md), ignoring a specific rule is as simple as adding an additional attribute to a function or script:

{% highlight powershell %}
function SuppressMe()
{
    [Diagnostics.CodeAnalysis.SuppressMessageAttribute("PSProvideCommentHelp", "")]
    param()

    Write-Verbose -Message "I'm making a difference!"

}
{% endhighlight %}

Should be no problem, right? Just add that attribute right before our...function's...param block...

Wait, what about a Pester test?

My first instinct was to try to add the rule to the ScriptBlock where the call to ConvertTo-SecureString was located. After all, ScriptBlocks can accept parameters, so why couldn't they accept attributes as well? In this particular case, the ScriptBlock was a Context block in Pester:

{% highlight powershell %}
Context "Behavior testing" {

    # To be clear: this doesn't work as expected.
    [Diagnostics.CodeAnalysis.SuppressMessageAttribute("PSAvoidUsingConvertToSecureStringWithPlainText", "")]
    param()

    $testUri = 'http://example.com'
    $testUsername = 'testUsername'
    $testPassword = 'password123'
    $testCred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $testUsername,(ConvertTo-SecureString -AsPlainText -Force $testPassword)

    # ...
}
{% endhighlight %}

Unfortunately, when I ran Invoke-ScriptAnalyzer again, it returned the same three errors.

After some thought, I realized the logical error in my thinking: PSScriptAnalyzer doesn't actually invoke the code. It's a "static code checker," so it uses text parsing rather than code execution to identify potential errors. This means that the parser isn't coded to recognize Pester keywords (Describe, Context, It) in the same way it recognizes the function keyword.

I moved the attribute to the very start of the .Tests.ps1 file and added a helpful comment for future me:

{% highlight powershell %}
# PSScriptAnalyzer - ignore creation of a SecureString using plain text for the contents of this script file
[Diagnostics.CodeAnalysis.SuppressMessageAttribute("PSAvoidUsingConvertToSecureStringWithPlainText", "")]
param()


$here = Split-Path -Parent $MyInvocation.MyCommand.Path
$sut = (Split-Path -Leaf $MyInvocation.MyCommand.Path).Replace(".Tests.", ".")
. "$here\$sut"
{% endhighlight %}

Now, back in my interactive PowerShell session, I ran the PSScriptAnalyzer again.

{% highlight powershell %}
PS> Invoke-ScriptAnalyzer -Path D:\Documents\Projects\PSJira -Recurse -Severity Error

RuleName                            Severity     FileName   Line  Message
--------                            --------     --------   ----  -------
PSAvoidUsingConvertToSecureStringWi Error        New-JiraSe 14    File 'New-JiraSession.Tests.ps1' uses
thPlainText                                      ssion.Test       ConvertTo-SecureString with plaintext. This will expose
                                                 s.ps1            secure information. Encrypted standard strings should be
                                                                  used instead.
PSAvoidUsingConvertToSecureStringWi Error        Remove-Jir 14    File 'Remove-JiraSession.Tests.ps1' uses
thPlainText                                      aSession.T       ConvertTo-SecureString with plaintext. This will expose
                                                 ests.ps1         secure information. Encrypted standard strings should be
                                                                  used instead.

{% endhighlight %}

Success! I'm down to two errors, instead of three. That means the attribute was successful, and the script analyzer is ignoring that rule for that single file.

I also ran ```Invoke-Pester``` again, just to confirm that it correctly loaded and ran the test. As I thought, adding a param() block to the start of the file didn't cause any harm.

After copying and pasting these lines into the other two files causing the same error, I finally receive blessed silence:

{% highlight powershell %}
PS> Invoke-ScriptAnalyzer -Path D:\Documents\Projects\PSJira -Recurse -Severity Error

PS>
{% endhighlight %}

# Conclusion

Again, I think it's awesome that Microsoft is taking the initiative to contact module publishers when their files aren't up to snuff. This demonstrates that they're committed to maintaining quality code in the PowerShell Gallery.

It might be nice if PSScriptAnalyzer provided support for attributes in Pester code blocks - or really, any arbitrary ScriptBlock - but that's not a huge concern since it's so easy to exclude that rule for the context of the file. I'd caution against just blindly adding exclusions to your projects, though, as that defeats the whole purpose of PSScriptAnalyzer in the first place.

Now, to see about those other warnings...