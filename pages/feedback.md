---
layout: page
title: Feedback
permalink: /feedback/
---

Saml feedback fra undervisere og medstuderende. Tilføj egne noter om, hvad du ændrer baseret på feedbacken.

<ul>
{% for post in site.feedback %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <small>— {{ post.date | date: "%Y-%m-%d" }}</small>
  </li>
{% endfor %}
</ul>
