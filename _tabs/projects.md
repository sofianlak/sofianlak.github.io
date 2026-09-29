---
layout: page
icon: fas fa-diagram-project
order: 2
title: Projects
description: A curated selection of projects built and maintained by Sofian Lakhdar.
---

<ul>
  {% for project in site.data.projects %}
  <li>
    <a href="{{ project.url | escape }}">{{ project.title | escape }}</a>
    <br>
    <strong>Why I built that?</strong> {{ project.problem | escape }}
  </li>
  {% endfor %}
</ul>
