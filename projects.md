---
layout: page
title: Projects
description: Various projects created over the years
---

<style type="text/css">
.tg td, .tg th {
  border-color: #00000000;
  overflow: hidden;
  padding: 0px 3px;
  word-break: normal;
}
.tg th {
  font-weight: normal;
}
.tg .tg-mrss {
  border-color: #00000000;
  font-family: inherit;
  text-align: left;
  vertical-align: center;
}
.tg .tg-zbu7 {
  border-color: #00000000;
  font-family: inherit;
  text-align: left;
  vertical-align: top;
}
</style>

{% for item in site.data.projects %}
# **{{ item.name }}**
#### *{{ item.start }} - {{ item.end }}*

<table class="tg">
  <thead>
    <tr>
  {% assign remainder = forloop.index | modulo: 2 %}
      {% if remainder == 0 %}
        <td class="tg-zbu7">
          {% if item.primary-image %}
            <br><img src="../img/{{ item.primary-image }}" width="8000">
          {% elsif item.primary-video %}
            <br><video width="400" controls>
              <source src="../img/{{ item.primary-video }}" type="video/mp4">
            </video>
          {% endif %}
        </td>
        <td class="tg-mrss">{{ item.description }}
        <br><br>
        <p><a href="{{ item.itch }}"><strong>Play on Itch.io</strong></a></p>
        </td>
      {% else %}
        <td class="tg-mrss">{{ item.description }}
        <br><br>
        <p><a href="{{ item.itch }}"><strong>Play on Itch.io</strong></a></p>
        </td>
        <td class="tg-zbu7">
          {% if item.primary-image %}
            <br><img src="../img/{{ item.primary-image }}" width="8000">
          {% elsif item.primary-video %}
            <br><video width="400" controls>
              <source src="../img/{{ item.primary-video }}" type="video/mp4">
            </video>
          {% endif %}
        </td>
      {% endif %}
    </tr>
  </thead>
</table>

{% endfor %}

----

{% if site.comments_repo %}
{% include comments.html %}
{% endif %}
