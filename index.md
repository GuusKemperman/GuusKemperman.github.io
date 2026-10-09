---
layout: page
title: 
---

{%- assign resume = site.data.resume -%}

<section class="intro">
  <div class="intro-content">
    <div class="image-wrapper">
      <img src="/img/resume/profilepic2.jpg" alt="Guus Kemperman" class="intro-image">
    </div>
    <div class="intro-text">
      <h1>{{ resume.header.name }}</h1>
      <h2>{{ resume.header.current_title }}</h2>
      <p>{{ resume.header.intro }}</p>
      <div class="skills-container">
      {% for skill in site.data.skills %}
        {% if skill.link %}
          <a href="{{ skill.link }}" class="skill-item">
        {% endif %}
        
        <div class="skill-item">
          <div class="skill-icon{% if skill.tags contains 'white-bg' %} white-bg{% endif %}">
            <picture>
              <!-- Show dark icon in dark mode -->
              <source srcset="{{ skill.icon-dark }}" media="(prefers-color-scheme: dark)">
              <!-- Show light icon in light mode -->
              <source srcset="{{ skill.icon-light }}" media="(prefers-color-scheme: light)">
              <!-- Fallback for browsers that don't support picture element -->
              <img src="{{ skill.icon-darklight }}" alt="{{ skill.skill }} icon" class="skill-svg">
            </picture>          </div>
          <div class="skill-description">
            <h4>{{ skill.skill }}</h4>
            <p>{{ skill.description }}</p>
          </div>
        </div>

        {% if skill.link %}
          </a>
        {% endif %}
      {% endfor %}
</div>

      <a href="/assets/GuusKemperman.pdf" class="read-more" draggable="false" selectable="false">Resume</a>
    </div>
  </div>
</section>

<!-- Add this after the intro section -->
<section class="experience-section">
  <div class="experience-grid">
    {% for entry in site.data.experience %}
      <div class="experience-card">
        <div class="experience-header">
          <h1><strong>{{ entry.company }}</strong></h1>
          <p><strong>{{ entry.role }}</strong></p>
          <p class="experience-dates">{{ entry.start }} - {{ entry.end }}</p>
        </div>
        <div class="experience-description">
          <p>{{ entry.description }}</p>
        </div>
      </div>
    {% endfor %}
  </div>
</section>

<div class="project-grid">
  {% for item in site.data.projects %}
    {% assign link = item.details_link | default: item.game_link | default: item.code_link %}

    <a href="{{ link }}" class="project-card{% if item.tags contains 'highlighted' %} highlighted{% endif %}">
      <h1><strong>{{ item.name }} ({{ item.end }})</strong></h1>

      {% if item.thumbnail-img %}
        <img class="project-media" src="{{ item.thumbnail-img }}">
      {% elsif item.thumbnail-vid %}
      {% include looping-video.html src=item.thumbnail-vid %}
      {% endif %}

      <h4>{{ item.brief-description }}</h4>

      <div class="project-tags">
        <p><strong>Contributions:</strong> {{ item.contribution }}</p>
        <p><strong>Engine/Tools:</strong> {{ item.engine }}</p>
        
        <p><strong>Team:</strong>

        {% if item.team_size %}
            {{ item.team_size }}
         {% else %}
            {% if item.num_programmers %}
              {{ item.num_programmers }} Programmers
            {% endif %}

            {% if item.num_designers %}
              | {{ item.num_designers }} Designers
            {% endif %}

            {% if item.num_artists %}
              | {{ item.num_artists }} Artists
            {% endif %}
        {% endif %}
        </p>
        <p><strong>Platforms:</strong> {{ item.platforms }}</p>
        <p><strong>Duration:</strong> {{ item.duration }}</p>
      </div>
    </a>
  {% endfor %}
</div>