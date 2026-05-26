---
layout: page
title: News
permalink: /news/
nav: false
description: An archive of announcements and updates.
---

<div class="news">
  {% if site.news != blank -%}
  {%- assign news = site.news | reverse -%}
  {%- assign current_year = "" -%}
  {%- for item in news -%}
    {%- assign item_year = item.date | date: "%Y" -%}
    {%- if item_year != current_year -%}
      {%- if current_year != "" %}
        </table>
      </div>
      {% endif -%}
      <h2 class="year">{{ item_year }}</h2>
      <div class="table-responsive">
        <table class="table table-sm table-borderless">
      {%- assign current_year = item_year -%}
    {%- endif %}
      <tr>
        <th scope="row">{{ item.date | date: "%b %-d, %Y" }}</th>
        <td>
          {% if item.inline -%}
          {{ item.content | remove: '<p>' | remove: '</p>' | emojify }}
          {%- else -%}
          <a class="news-title" href="{{ item.url | relative_url }}">{{ item.title }}</a>
          {%- endif %}
        </td>
      </tr>
  {%- endfor %}
        </table>
      </div>
  {%- else -%}
  <p>No news so far...</p>
  {%- endif %}
</div>
