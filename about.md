---
layout: page
title: About
---

{% comment %}
  This inserts the "about" photo and text from `_config.yml`.
  You can edit it there (jekyll needs restart!) or remove it and provide your own photo/text.
  Don't forget to add the `me` class to the photo, like this: `![alt](src){:.me}`.
{% endcomment %}

{% if site.author.photo %}
  ![{{ site.author.name }}]({{ site.author.photo }}){:.me}
{% endif %}

{{ site.author.about }}

## What is this place?

You find a blog.  Roll Perception.

22? Nice roll. Okay, here's what you can tell:

This is a blog primarily devoted to PowerShell, software deployment, and system automation, though other topics of interest may come up as I find them interesting.  I tend to write about things that I deal in my current work position, so my topics of interest will likely change over time.

## What's your profile picture?

That's Luke fon Fabre, from the excellent game Tales of the Abyss. I consider this my favorite video game of all time.