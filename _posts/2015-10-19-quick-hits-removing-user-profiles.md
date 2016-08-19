---
layout: post
title: "Quick Hits: Removing User Profiles"
excerpt: "Removing user profiles from ALL the machines!"
tags: [posh]
---

# Table of Contents

* TOC
{:toc}

# Introduction

Today, I was tasked with a strange one: I needed to remove all existing user profiles from all machines in an Active Directory OU.  This was for a public use lab that had built up over 50 user profiles on some accounts.

Since I wanted to work with machines in parallel, I decided it was time to stretch my workflow legs.

# Code

{% highlight powershell %}
Start-Transcript -Path C:\posh_deleteUserAccounts.log

workflow DeleteUserAccounts
{
    parallel
    {
        InlineScript
        {
            # Get-CimInstance -ComputerName $PSComputerName -ClassName Win32_UserProfile | Remove-CimInstance -Verbose
            Get-WmiObject -ComputerName $PSComputerName -Class Win32_UserProfile | Remove-WmiObject -Verbose
        }
    }
}

# Get a reference to all computer names in the target OU
$computers = Get-ADComputer -Filter * -SearchBase "OU=Labs,DC=example,DC=com" | Select-Object -ExpandProperty Name

# Run our workflow
DeleteUserAccounts -PSComputerName $computers
{% endhighlight %}

# Breaking it down
First and foremost, Start-Transcript is a handy way to log PowerShell output to a file.  Since we'll be working on multiple computes simultaneously, this is a quick and easy way to capture all PowerShell's output to a file.  Think of it as a poor man's log file.

The next few lines define a PowerShell workflow, a parallel block, and an InlineScript within the parallel block.  This establishes that the contents of the InlineScript will be run in parallel against each of the computers we specify later.

The $PSComputerName variable is an automatic variable created by the PowerShell workflow engine.  This will contain a reference to the current computer name of the machine we're operating against.

We use Get-WmiObject and Remove-WmiObject to get all instances of the Win32_UserProfile WMI class on the target machine, and remove them.  Remove-WmiObject causes different effects based on the target WMI class - for example, invoking Remove-WmiObject on an instance of the Win32_Printer class would - you guessed it - remove the printer from the system.  In this case, it removes the user profile.

I began this script by trying to use the CIM cmdlets introduced in PowerShell 3.  Unfortunately, I realized that while my management machine had access to these cmdlets, my target computers were still runinng PowerShell 2, so I needed to fall back on the "old" WMI cmdlets.

Finally, we obtain a reference to the names of all computers in our target OU, and invoke the workflow.  Get-ADComputer -Filter * is a BAD idea if run against a large OU - or worse, if run without the SearchBase parameter at all - because it runs a very large LDAP query against your domain controllers.  In this case, my target OU only contained about 15 computers, so the -SearchBase parameter was enough to limit it to a reasonable number.

The -PSComputerName parameter of our workflow is an automatic parameter added by the PowerShell workflow engine - just like the $PSComputerName variable earlier.  This allows us to specify a list of computer names to this parameter, and PowerShell will handle all the details.  It will check network connectivity in the background and handle running the workflow as defined.

# Further reading

For more information on using workflows in PowerShell, I'd recommend these articles:

[PowerShell Workflows - PowerShell Magazine](http://www.powershellmagazine.com/2012/11/14/powershell-workflows/)

PowerShell Workflow for Mere Mortals - 5-part series:
* [Part 1](http://blogs.technet.com/b/heyscriptingguy/archive/2013/08/19/powershell-workflow-for-mere-mortals-part-1.aspx)
* [Part 2](http://blogs.technet.com/b/heyscriptingguy/archive/2013/08/20/powershell-workflow-for-mere-mortals-part-2.aspx)
* [Part 3](http://blogs.technet.com/b/heyscriptingguy/archive/2013/08/21/powershell-workflow-for-mere-mortals-part-3.aspx)
* [Part 4](http://blogs.technet.com/b/heyscriptingguy/archive/2013/08/21/powershell-workflow-for-mere-mortals-part-4.aspx)
* [Part 5](http://blogs.technet.com/b/heyscriptingguy/archive/2013/08/23/powershell-workflow-for-mere-mortals-part-5.aspx)

-replica