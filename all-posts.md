---
layout: page
title: All Posts
permalink: /all-posts/
---

<div class="all-posts">
  <h1>All Posts</h1>
  
  <ul class="post-list">
    {% for post in site.posts %}
      <li class="post-item">
        <div class="post-title">
          <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
        </div>
        <div class="post-meta">
          <span class="post-date">{{ post.date | date: "%b %-d, %Y" }}</span>
          {% if post.categories.size > 0 %}
          <span class="post-categories">
            in 
            {% for category in post.categories %}
              <a href="{{ site.baseurl }}/categories/{{ category | slugify }}/">{{ category }}</a>{% unless forloop.last %}, {% endunless %}
            {% endfor %}
          </span>
          {% endif %}
        </div>
        {% if site.show_excerpts %}
          <div class="post-excerpt">
            {{ post.excerpt }}
          </div>
        {% endif %}
      </li>
    {% endfor %}
  </ul>
</div>