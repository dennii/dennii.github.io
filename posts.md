---
layout: page
title: Posts
---

## Posts

This is an archive of my posts.

<ul>
  {% for post in site.posts %}
  {% if post.categories contains 'blog' %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endif %}
  {% endfor %}
</ul>

## Data Dispatch

This is an archive of my posts for [Data Dispatch](/data-dispatch/). It is only periodically updated, so check the
X channel [@\_data_dispatch\_](https://x.com/_data_dispatch_) for more recent posts.

<ul>
  {% for post in site.posts %}
  {% if post.categories contains 'data-dispatch' %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endif %}
  {% endfor %}
</ul>
