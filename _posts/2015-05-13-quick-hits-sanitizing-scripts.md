---
layout: post
title: "Quick Hits: Sanitizing Scripts"
excerpt: "Identifying sensitive information that needs to be removed"
tags: [posh]
---

# Table of Contents

* TOC
{:toc}

I've recently been polishing up a PowerShell module to prepare to publish it on GitHub, and I've needed to remove a lot of organizational information from the module. It's amazing how easy it is to hard-code things like server names and credentials when you're writing code for your own use!

The trick was that my module is broken into several different script files and [Pester tests](https://github.com/pester/Pester). How can I identify specific files and locations where I have sensitive data I need to remove?

# Select-String to the rescue!

{% highlight powershell %}
Select-String -Path C:\Scripts\myscript.ps1 'myserver.example.com'
{% endhighlight %}
[![This command returns the filename, line number, and text where the string "myserver.example.com" was found.]({{ site.url }}/images/blog/2015-05-13-quick-hits-sanitizing-scripts/ps1.png)]({{ site.url }}/images/blog/2015-05-13-quick-hits-sanitizing-scripts/ps1.png)

I can search multiple files by piping the output of Get-Item or Get-ChildItem into the InputObject parameter of Select-String:

{% highlight powershell %}
Get-ChildItem -Path C:\Scripts -Recurse | Select-String 'myserver'
{% endhighlight %}
[![This command returns multiple filenames, with the lines where each instance of "myserver" was found.]({{ site.url }}/images/blog/2015-05-13-quick-hits-sanitizing-scripts/ps2.png)]({{ site.url }}/images/blog/2015-05-13-quick-hits-sanitizing-scripts/ps2.png)

The third result in this example gave me a Describe block in a Pester test. If I had really named my function with a company server name, I would definitely have some work to do before publishing!

Speaking of Pester, want to search only your Pester tests?

{% highlight powershell %}
Get-ChildItem -Path C:\Scripts -Recurse -Include '*.Tests.ps1' | Select-String 'myserver.example.com'
{% endhighlight %}
[![This command only returns one file, since I only have one Pester test in my demo.]({{ site.url }}/images/blog/2015-05-13-quick-hits-sanitizing-scripts/ps3.png)]({{ site.url }}/images/blog/2015-05-13-quick-hits-sanitizing-scripts/ps3.png)

-replica