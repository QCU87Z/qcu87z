---
layout: post
title:  "Jekyll and docker"
date:   2020-01-20 05:00:00 +1100
categories: jekyll docker
---

This is how I am creating new content on this site using Jekyll and docker. If I wasn't using this I would have to push to git to much and I loose my ability to have drafts or I would have to have a local ruby environment.

I have a docker-compose file which copies in the github repo and enables auto refresh

{% highlight yaml %}
version: '3'

services:
  jekyll:
    image: jekyll/jekyll
    container_name: blog-qcu87z
    environment:
        - JEKYLL_ENV=docker
    # force_polling makes the linux box watch for any changes to files, then it will regenerate
    # livereload gets the browser to automatically refresh when changes happen to files
    command: jekyll serve --force_polling --livereload
    ports:
        - 4000:4000
        - 35729:35729
    volumes:
        - ./_site:/srv/jekyll

{% endhighlight %}

The use 

> docker-compose up -d

Then the site is available on port 4000
