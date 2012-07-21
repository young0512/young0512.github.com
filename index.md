---
layout: page
title: Young's Blog
tagline: "进步每一天"
description: ""
keywords: ""
group: navigation
weight : 1
nav_tag : "首页"
---


### Blog Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

