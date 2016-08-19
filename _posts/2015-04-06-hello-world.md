---
layout: post
title: Hello World
---

# Table of Contents

* TOC
{:toc}

# Introduction

Well, here we are. It's been a rocky road getting this all figured out, but I think we're finally there.

Installing Jekyll with the [Skinny Bones theme](https://mmistakes.github.io/skinny-bones-jekyll/getting-started/) is actually quite simple, even on Windows.

# Getting started with Jekyll on a Windows system

1. Create a GitHub repository. Name it according to your username: **username.github.io**.  This will also be the URL of your blog.
2. Clone your new repository to your working machine using Git.
3. Download the [Skinny Bones theme](https://github.com/mmistakes/skinny-bones-jekyll/archive/master.zip) and extract it to your newly-cloned Git repo. It may overwrite the `readme.md` file, which is fine.
4. Install Ruby
    1. Download the latest build of the [RubyInstaller project](http://rubyinstaller.org/downloads/) and install.
    2. Grab the latest version of the Development Kit from the same page and extract (to a path without spaces).
    3. In a command prompt, navigate to the extracted folder: `cd C:\RubyDevKit`
    4. Auto-detect Ruby installations and add them to a configuration file: `ruby dk.rb init`
        * On some 64-bit systems, this will come back and complain that no versions of Ruby were detected.  If so, check out [a related StackOverflow question].  You'll basically need to edit the script to add an extra Registry location to the locations it checks and re-run the script.
    5. Install (register) the DevKit: `ruby dk.rb install`
5. Download and install the latest [Python 2.7 installer](https://www.python.org/downloads/).
    * Note that Python 3 is not compatible with Jekyll and Pygments at this time, so don't download that one.
6. Back in a console window, install Bundler for Ruby: `gem install bundler`
7. Still in the console, navigate to the directory where the Skinny Bones theme was extracted (your repo for the blog).
8. Run `bundle install` to install all needed dependencies.  This will install Jekyll and Octopress.
9. Edit the `_config.yml` file.  You'll need to customize the url of the blog, and anything else that looks interesting.
10. Edit the `_data\navigation.yml` file. This is the navigation header that will appear at the top of your blog.
11. Commit your changes and push back to GitHub.

### Octopress usage

You can see a full list of Octopress commands at its [official GitHub page](https://github.com/octopress/octopress).

Here are some of the more commonly-used Octopress commands:


    octopress new draft "My New Post"                        # Creates a new draft with the post template

    octopress publish "My New Post"                          # Publishes the draft we just created

    octopress unpublish "My New Post"                        # Moves the post back to draft status


# References

Jekyll by default uses [Kramdown markup](http://kramdown.gettalong.org/syntax.html).

Here's a [GitHub markdown cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) that I found to be rather useful.

# Testing syntax highlighting

Here's some PowerShell code to play with.

{% highlight powershell %}
# This is a comment
$rand = Get-Random -Min 1 -Max 10
if ($rand -gt 5)
{
    Write-Output "My random number is greater than 5!"
} else {
    Write-Output "My random number is 5 or less."
}

$props = @{
    Number = $rand;
    String = "A string";
}

Write-Output (New-Object -TypeName PSObject -Property $props)
{% endhighlight %}