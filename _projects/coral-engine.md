---
layout: project
name: "Coral Engine"
---

<hr>
<div style="text-align: center; font-size: 1.5em; font-style: italic; margin: 20px 0;">
    "Best Engine to come out of BUAS yet!"
</div>
<div style="text-align: center; font-size: 1em; font-style: normal; margin: 5px 0;">
    Bojan Endrovski, Lecturer @ Breda University of Applied Sciences
</div>
<hr>

Coral Engine began as a personal project during a summer holiday. Over time, it evolved into a full-fledged engine featuring **visual scripting** and a **robust editor**. From the start, the goal was to create a simple, accessible API for developers while ensuring the engine remained intuitive for designers.

After seven months of solo development, I was joined by five talented programmers to enhance Coral Engine, making it production-ready. We used it to develop [Lichgate](/projects/lichgate). The designers who joined our team were able to utilise its capabilities to create their own prototypes, assets and gameplay features.

I continued working on Coral Engine for several months, ridding it of some technical debt that had built up over the course of development.

<hr>
<div style="text-align: center; font-size: 1.5em; font-style: italic; margin: 20px 0;">
    "It's incredibly impressive what was achieved in such a short amount of time!"
</div>
<div style="text-align: center; font-size: 1em; font-style: normal; margin: 5px 0;">
    Xan Hisam, Associate Tech Designer @ Splash Damage
</div>
<hr>

# <span style="display: block; text-align: center;">My Contributions</span>

<div class="project-grid">
  {% for item in site.data.coral-contributions %}
    {% assign link = item.details_link %}

   {% if link %}
  <a href="{{ link }}" class="project-card narrow{% if item.tags contains 'highlighted' %} highlighted{% endif %}">
{% else %}
  <div class="project-card narrow no-link {% if item.tags contains 'highlighted' %} highlighted{% endif %}">
{% endif %}

  <h1>{{ item.name }}</h1>

  {% if item.thumbnail-img %}
    <br><img class="project-media" src="{{ item.thumbnail-img }}">
  {% elsif item.thumbnail-vid %}
  {% include looping-video.html src=item.thumbnail-vid %}
  {% endif %}
  
  <p>{{ item.description }}</p>

{% if link %}
  </a>
{% else %}
  </div>
{% endif %}

  {% endfor %}
</div>


# Games Created with Coral Engine

- [Lichgate](/projects/lichgate)

    
