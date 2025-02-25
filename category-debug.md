---
layout: page
title: Category Debug
permalink: /category-debug/
---

<h2>All Categories in Site</h2>

<ul>
{% for category in site.categories %}
  <li>
    <strong>{{ category[0] }}</strong> ({{ category[1].size }} posts)
  </li>
{% endfor %}
</ul>

<h2>All Posts with Categories</h2>

<ul>
{% for post in site.posts %}
  <li>
    <a href="{{ post.url }}">{{ post.title }}</a>
    <br>
    Categories: 
    {% if post.categories.size > 0 %}
      {% for category in post.categories %}
        <span style="background-color: #f0f0f0; padding: 2px 8px; border-radius: 4px; margin-right: 5px;">
          {{ category }}
        </span>
      {% endfor %}
    {% else %}
      <em>No categories</em>
    {% endif %}
  </li>
{% endfor %}
</ul>