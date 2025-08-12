---
layout: page
title: Projekter
permalink: /projects/
---

Her samler jeg projekter. Hvert projekt ligger som en side i mappen `_projects/`.

**Forslag (opret filer i `_projects/`):**

- `ci-cd-pipeline.md` – Byg & deploy pipeline med GitHub Actions.
- `infra-as-code.md` – Terraform-projekt til cloud-infrastruktur.
- `scripting-automation.md` – Automatisering af gentagne udviklingsopgaver.

<ul>
{% for post in site.projects %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <small>— {{ post.date | date: "%Y-%m-%d" }}</small>
  </li>
{% endfor %}
</ul>
