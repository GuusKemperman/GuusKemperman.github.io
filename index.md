---
layout: page
title: 
description: Portfolio of Guus Kemperman, an engine programmer specializing in scripting and runtime reflection.
---

{%- assign resume = site.data.resume -%}

<section class="intro">
  <div class="intro-content">
    <div class="image-wrapper">
      <img src="/img/resume/profilepic2.jpg" alt="Guus Kemperman" class="intro-image">
    </div>
    <div class="intro-text">
      <h1>{{ resume.header.name }} {{ resume.header.suffix }}</h1>
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

    <a href="{{ link }}" class="project-card{% if item.tags contains 'highlighted' %} highlighted{% endif %}" 
       style="border: 1px solid #ddd; padding: 20px; border-radius: 10px; background-color: #f9f9f9; position: relative; display: block; text-decoration: none; transition: transform 0.2s;">
      <h1><strong>{{ item.name }} ({{ item.end }})</strong></h1>

      {% if item.thumbnail-img %}
        <br><img class="project-media" src="{{ item.thumbnail-img }}" style="width: 100%; height: auto;">
      {% elsif item.thumbnail-vid %}
      <div class="video-as-gif-container">
        <video autoplay loop muted playsinline>
          <source src="{{ item.thumbnail-vid }}" type="video/mp4">
        </video>
      </div>
      {% endif %}

      <h4>{{ item.brief-description }}</h4>

      <div class="project-tags">
        <p><strong>Contributions:</strong> {{ item.contribution }}</p>
        <p><strong>Engine/Tools:</strong> {{ item.engine }}</p>
        
        <p><strong>Team Size:</strong>

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
        {% if project.code_link %}
        <div class="tag">
            <svg class="github-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="currentColor">
              <path d="M12 0C5.37 0 0 5.37 0 12c0 5.3 3.438 9.8 8.205 11.385.6.112.82-.26.82-.577 0-.285-.01-1.04-.015-2.04-3.338.725-4.043-1.61-4.043-1.61-.546-1.387-1.333-1.757-1.333-1.757-1.09-.745.083-.73.083-.73 1.204.084 1.837 1.236 1.837 1.236 1.07 1.835 2.807 1.305 3.492.998.108-.776.418-1.305.76-1.605-2.665-.303-5.466-1.333-5.466-5.93 0-1.31.47-2.38 1.236-3.22-.124-.303-.535-1.523.117-3.176 0 0 1.008-.322 3.3 1.23a11.49 11.49 0 0 1 3.003-.404c1.02.005 2.047.138 3.003.403 2.29-1.553 3.298-1.23 3.298-1.23.653 1.653.242 2.873.118 3.176.767.84 1.235 1.91 1.235 3.22 0 4.61-2.807 5.624-5.479 5.92.43.37.813 1.1.813 2.22 0 1.605-.015 2.898-.015 3.292 0 .32.217.694.825.576C20.565 21.797 24 17.3 24 12 24 5.37 18.63 0 12 0z"/>
            </svg>
            <a href="{{ project.code_link }}">GitHub</a>
          </div>
        {% endif %}
      </div>
    </a>
  {% endfor %}
</div>


----

