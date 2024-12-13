---
layout: page
title: 
description: Various projects created over the years
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

    <a href="/resume.html" class="read-more">Read more</a>

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
      <h4><em>{{ item.brief-description }}</em></h4>

      {% if item.primary-image %}
        <br><img class="project-media" src="../img/{{ item.primary-image }}" style="width: 100%; height: auto;">
      {% elsif item.primary-video %}
      <div class="video-as-gif-container">
        <video autoplay loop muted playsinline>
          <source src="../img/{{ item.primary-video }}" type="video/mp4">
        </video>
      </div>
      {% endif %}

      <div class="project-tags">
        <p>🔧 <strong>Engine/Tools:</strong> {{ item.engine }}</p>
        <p>👥 <strong>Team Size:</strong> {{ item.team_size }}</p>
        <p>💻 <strong>Platforms:</strong> {{ item.platforms }}</p>
        <p>⏳ <strong>Duration:</strong> {{ item.duration }}</p>
        <p>🧠 <strong>Contribution:</strong> {{ item.contribution }}</p>
      </div>
    </a>
  {% endfor %}
</div>


----

{% if site.comments_repo %}
{% include comments.html %}
{% endif %}
