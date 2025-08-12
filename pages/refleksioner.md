---
layout: page
title: Refleksioner
permalink: /refleksioner/
---

Løbende refleksioner om læring, fremskridt og udfordringer. Brug evt. blogindlæg i `_posts/` for dato-stemplede refleksioner.


<ul>
{% raw %}{% for post in site.posts %}{% endraw %}
  <li>
    <a href="{% raw %}{{ post.url | relative_url }}{% endraw %}">
      {% raw %}{{ post.title }}{% endraw %}
    </a>
    <small>— {% raw %}{{ post.date | date: "%Y-%m-%d" }}{% endraw %}</small>
  </li>
{% raw %}{% endfor %}{% endraw %}
</ul>
