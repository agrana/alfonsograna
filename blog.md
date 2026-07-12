---
layout: page
title: "Articles"
permalink: /blog/
---

<h1>Articles</h1>

<ul>
  {% for post in site.posts %}
  <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
