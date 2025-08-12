---
layout: page
title: Ressourcer
permalink: /ressourcer/
---

- Litteratur, artikler og dokumentation
- Repositories og kodeeksempler
- Cheatsheets

<ul>
{% for post in site.resources %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <small>— {{ post.date | date: "%Y-%m-%d" }}</small>
  </li>
{% endfor %}
</ul>
