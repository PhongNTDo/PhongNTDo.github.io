---
layout: single
permalink: /blog/
title: "Blog"
author_profile: false
sidebar:
  - title: "All Posts"
    type: "blog_posts"
---

Welcome to my blog, where I share my thoughts on Artificial Intelligence and Geography. Below is a list of all my posts.

{% for post in site.posts reversed %}
  {% include archive-single.html show_excerpt=false %}
{% endfor %}