---
layout: page
title: "The Blog"
permalink: /blog/
---

# Collection of Posts

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>