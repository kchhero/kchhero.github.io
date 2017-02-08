---
bg: archive.jpg
layout: page
permalink: /_posts/
title: "Archive"
crawlertitle: "All articles"
active: archive
---

{% for tag in site.tags %}
  {% assign t = tag | first %}
  {% assign posts = tag | last %}

<h2 class="category-key" id="{{ t | downcase }}">{{ t | capitalize }}</h2>

<ul class="year">
  {% for post in posts %}
    {% if post.tags contains t %}
      <li>
        {% if post.lastmod %}
          <a href="{{ post.url }}" style="color:blue">{{ post.title }}</a>
          <span class="date">{{ post.lastmod | date: "%d-%m-%Y"  }}</span>
        {% else %}
          <a href="{{ post.url }}" style="color:blue">{{ post.title }}</a>
          <span class="date">{{ post.date | date: "%d-%m-%Y"  }}</span>
        {% endif %}
      </li>
    {% endif %}
  {% endfor %}
</ul>


{% endfor %}