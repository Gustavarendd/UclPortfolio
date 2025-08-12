---
layout: home
title: Velkommen
---

Denne portefølje samler mit arbejde i efterårssemesteret 2025 inden for **Automatisering & Scripting** og **Cloud Computing & DevOps**.

- 📁 [Projekter](/projects/)
- 📝 [Refleksioner](/refleksioner/)
- 💬 [Feedback](/feedback/)
- 🔗 [Ressourcer](/ressourcer/)
- 🎯 [Mål](/maal/)



<ul>
{% for post in site.posts %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <small>— {{ post.date | date: "%Y-%m-%d" }}</small>
  </li>
{% endfor %}
</ul>
