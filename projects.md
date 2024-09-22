---
layout: page
title: Projects
description: Various projects created over the years
---

<div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 20px;">
  {% for item in site.data.projects %}
    <div class="project-card" style="border: 1px solid #ddd; padding: 20px; border-radius: 10px; background-color: #f9f9f9;">
      <h1><strong>{{ item.name }} ({{ item.end }})</strong></h1>
      <h4><em>{{ item.brief-description }}</em></h4>
        {% if item.primary-image %}
            <br><img class="project-media" src="../img/{{ item.primary-image }}">
        {% elsif item.primary-video %}
            <br><video class="project-media" controls>
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
    </div>
  {% endfor %}
</div>

----

{% if site.comments_repo %}
{% include comments.html %}
{% endif %}
