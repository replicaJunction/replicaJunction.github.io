---
layout: post
title: Blog Rebase
excerpt: Technical upgrades
---

# Table of Contents

* TOC
{:toc}

# Introduction

After an extended hiatus, I've decided it's long past time I breathed some new life into this blog. My job and responsibilities have changed a bit, as have my interests, but PowerShell is still at the front of both!

# Rebase!

## Visual Updates

I was never quite happy with the visual style of my blog layout...while the Minimal Mistakes theme was a very clean and...well, minimal starting point, I never got it to a point where I felt confident in its polish.

To that end, the blog has been reworked to use the [Hydejacked](https://github.com/qwtel/hydejack) theme.

Hydejacked has a few less bells and whistles, but it presents a much cleaner and more polished look to my eyes. I also took some time to build a decent-looking sidebar image this time instead of using a generic image with no personality.

Compare the current look of this article to an older screenshot of the previous theme:

[![Old screenshot](/public/img/blog/2016-08-19-blog-rebase/old-screenshot.png)](/public/img/blog/2016-08-19-blog-rebase/old-screenshot.png)

The look still isn't perfect, but I like it a lot better!

Because of the nature of how Jekyll works, a theme is much more than a couple of config files - it's an entire framework for how the compiled Web pages should look. Switching themes isn't an intuitive process. In fact, I found that the easiest way was to create an entirely new Git repository with the new theme as a base, then copy my content from the original repo into the new one. Several header items needed to be adjusted, and the Table of Contents format also changed a bit.

It took a couple of hours, but I wouldn't say it was a difficult process so much as a tedious one. Fortunately, I hadn't yet written much to migrate!

## Process Updates

The biggest reason I haven't been writing more is that my process has just been tedious. Octopress is a fiddly tool, and I was always troubleshooting issues with version dependencies, working with Bundler to figure out which Octopress version I needed, or chasing my tail in some other issue. I never found myself using its draft or unpublish capability, either.

Instead, I've decided to abandon Octopress and use pure Jekyll for my blog-writing. Since Jekyll can serve pages locally, I have no real need to put pages in "draft" mode. Should I ever need to work on posts long-term where I absolutely need to push to GitHub, I can create a separate branch for the draft.

My writing process is now simple and effective:

* Create a markdown file in the _posts subdirectory
* Write the blog post
* Run ```jekyll serve -w```
* Open the page in Chrome (by default, ```http://127.0.0.1:4000```)
* Return to the markdown file and fix as needed
* Refresh Chrome. Jekyll should have rebuilt the page.
* If Jekyll failed to rebuild the page, kill the process and re-run it.
* Once I'm happy with the post, push back to GitHub and do a quick once-over to make sure it matches my local instance.

[![More regenerations than a Timelord](/public/img/blog/2016-08-19-blog-rebase/jekyll-regenerate.png)](/public/img/blog/2016-08-19-blog-rebase/jekyll-regenerate.png)

Finally, the [Rouge pigmenter now supports PowerShell](https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers), so there's no real reason to continue using Pygments. This means that my Jekyll instance no longer requires Python - it's all Ruby-based. This doesn't really affect me now, since I already have my dev environment working nicely, but I always like to see fewer moving parts where possible.

## A Note on Comments

Migrating to this theme did mean re-creating most of the content of the blog, and I've taken advantage of the opportunity to re-organize a little as well. Disqus does seem to have preserved most of the comments on the blog, but if there's anything missing from the comments section, I do apologize. My hope is that I shouldn't need to do this in-depth of a site rebuild again for the foreseeable future.

# Looking to the Future!

It may take a bit for the proverbial dust to settle. I love tweaking things to get them _juuuust right_, so I may continue playing with page and navigation styles for a while. Overall, though, I'm very happy to have brought this back to life, and I feel much better about the ease of writing more in the future.

See you all again next time!

~replica