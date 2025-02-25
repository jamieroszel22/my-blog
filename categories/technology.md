---
layout: page
title: Technology
permalink: /categories/technology/
---

<div class="category-buttons">
  <a href="/categories/python" class="category-button python">Python</a>
  <a href="/categories/ai" class="category-button ai">AI</a>
  <a href="/categories/technology" class="category-button technology">Technology</a>
  <a href="/categories/tutorial" class="category-button tutorial">Tutorial</a>
</div>

<h2>Technology Posts</h2>

<ul class="post-list">
  {% for post in site.posts %}
    {% if post.categories contains "Technology" or post.categories contains "technology" %}
      <li>
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        <span class="post-meta">{{ post.date | date: date_format }}</span>
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
          </a>
        </h3>
        
        <div class="post-categories">
          {%- if post.categories.size > 0 -%}
            {%- for category in post.categories -%}
              <span class="category-tag">{{ category }}</span>
            {%- endfor -%}
          {%- endif -%}
        </div>
        
        {%- if site.show_excerpts -%}
          {{ post.excerpt }}
        {%- endif -%}
      </li>
    {% endif %}
  {% endfor %}
</ul>