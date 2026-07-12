---
layout: page
icon: fas fa-diagram-project
order: 2
title: Projects
description: A curated selection of projects built and maintained by Sofian Lakhdar.
---

<p class="projects-intro">A curated selection of open source projects I am building, maintaining and learning from.</p>

{% if site.data.projects and site.data.projects.size > 0 %}
  <div class="projects-grid">
    {% for project in site.data.projects %}
      <article class="project-card">
        <header class="project-card__header">
          <i class="{{ project.icon }} project-card__icon" aria-hidden="true"></i>
          <h2 class="project-card__title">{{ project.title | escape }}</h2>
        </header>

        <p class="project-card__description">{{ project.description | escape }}</p>

        <ul class="project-card__tags" aria-label="Technologies used">
          {% for tag in project.tags %}
            <li>{{ tag | escape }}</li>
          {% endfor %}
        </ul>

        {% if project.links and project.links.size > 0 %}
          <nav class="project-card__links" aria-label="Links for {{ project.title | escape }}">
            {% for link in project.links %}
              <a href="{{ link.url | escape }}" target="_blank" rel="noopener noreferrer">
                <i class="{{ link.icon }}" aria-hidden="true"></i>
                <span>{{ link.label | escape }}</span>
              </a>
            {% endfor %}
          </nav>
        {% endif %}
      </article>
    {% endfor %}
  </div>
{% else %}
  <p class="projects-empty">Projects are being prepared. Please check back soon.</p>
{% endif %}
