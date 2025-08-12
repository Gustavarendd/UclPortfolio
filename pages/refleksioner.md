---
layout: page
title: Refleksioner
permalink: /refleksioner/
---

Løbende refleksioner om læring, fremskridt og udfordringer. Brug evt. blogindlæg i `_posts/` for dato-stemplede refleksioner.


<ul>
{% assign reflections = site.posts | where_exp: "p", "p.categories contains 'refleksion'" %}
{% for post in reflections %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <small>— {{ post.date | date: "%Y-%m-%d" }}</small>
  </li>
{% endfor %}
</ul>
