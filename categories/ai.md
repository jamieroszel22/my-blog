---
layout: page
title: AI
permalink: /categories/ai/
---

<div class="category-buttons">
  <a href="/categories/python" class="category-button python">Python</a>
  <a href="/categories/ai" class="category-button ai">AI</a>
  <a href="/categories/technology" class="category-button technology">Technology</a>
  <a href="/categories/tutorial" class="category-button tutorial">Tutorial</a>
</div>

<h2>AI Posts</h2>

<ul class="post-list">
  {% for post in site.posts %}
    {% if post.categories contains "AI" or post.categories contains "ai" %}
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

{% comment %}
Check if any posts were found
{% endcomment %}
{% assign found_posts = false %}
{% for post in site.posts %}
  {% if post.categories contains "AI" or post.categories contains "ai" %}
    {% assign found_posts = true %}
    {% break %}
  {% endif %}
{% endfor %}

{% if found_posts == false %}
  <p>No posts in this category yet.</p>
{% endif %}