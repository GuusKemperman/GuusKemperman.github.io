---
layout: page
title: 
description: Various projects created over the years
---

<div class="project-grid">
  {% for item in site.data.projects %}

    {% assign link = item.details_link %}
    {% if item.details_link %}
      {% assign link = item.details_link %}
    {% elsif item.game_link %}
      {% assign link = item.game_link %}
    {% elsif item.code_link %}
      {% assign link = item.code_link %}
    {% endif %}

    <a href="{{ link }}" class="project-card" style="border: 1px solid #ddd; padding: 20px; border-radius: 10px; background-color: #f9f9f9; position: relative; display: block; text-decoration: none; transition: transform 0.2s;">
      <h1><strong>{{ item.name }} ({{ item.end }})</strong></h1>
      <h4><em>{{ item.brief-description }}</em></h4>

        {% if item.primary-image %}
            <br><img class="project-media" src="../img/{{ item.primary-image }}" style="width: 100%; height: auto;">
        {% elsif item.primary-video %}
            <br><video class="project-media" controls style="width: 100%;">
              <source src="../img/{{ item.primary-video }}" type="video/mp4">
            </video>
        {% endif %}

        <div class="project-tags">
          <p>🔧 <strong>Engine/Tools:</strong> {{ item.engine }}</p>
          <p>👥 <strong>Team Size:</strong> {{ item.team_size }}</p>
          <p>💻 <strong>Platforms:</strong> {{ item.platforms }}</p>
          <p>⏳ <strong>Duration:</strong> {{ item.duration }}</p>
          <p>🧠 <strong>Contribution:</strong> {{ item.contribution }}</p>
        </div>

        {% if item.details_link %}
          <span class="details-indicator" style="position: absolute; top: 10px; right: 10px; background: #007bff; color: white; padding: 5px 10px; border-radius: 5px;">
            Read more
          </span>
        {% endif %}
    </a>

  {% endfor %}
</div>

----

{% if site.comments_repo %}
{% include comments.html %}
{% endif %}
