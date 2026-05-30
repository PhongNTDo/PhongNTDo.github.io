---
layout: single
permalink: /blog/
title: "Blog"
author_profile: false
sidebar:
  - title: "All Posts"
    type: "blog_posts"
---

{% assign posts = site.posts | sort: "date" | reverse %}

<div class="blog-page">
  <p class="blog-intro">
    Notes and essays on LLM workflows, agentic systems, retrieval-augmented generation, AI robustness, Vietnamese NLP, and GeoAI.
  </p>

  <div class="blog-list">
    {% for post in posts %}
      <article class="blog-card">
        <div class="blog-card-meta">
          <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %-d, %Y" }}</time>
          {% if post.tags %}
            <span class="blog-card-tags">
              {% for tag in post.tags %}
                {% if tag and tag != "" %}
                  <span>{{ tag }}</span>
                {% endif %}
              {% endfor %}
            </span>
          {% endif %}
        </div>
        <h2 class="blog-card-title">
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </h2>
        <p class="blog-card-excerpt">
          {{ post.excerpt | markdownify | strip_html | strip_newlines | truncatewords: 34 }}
        </p>
        <a class="blog-card-link" href="{{ post.url | relative_url }}">Read post</a>
      </article>
    {% endfor %}
  </div>
</div>
