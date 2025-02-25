---
layout: page
title: Python Posts
permalink: /categories/python/
---

<div class="category-page">
  <h1>Python Posts</h1>
  <ul class="post-list">
    {% for post in site.categories.python %}
      <li class="post-item">
        <div class="post-title">
          <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
        </div>
        <div class="post-meta">
          <span class="post-date">{{ post.date | date: "%b %-d, %Y" }}</span>
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

<style>
.category-page {
  margin: 2rem 0;
}

.post-list {
  list-style: none;
  padding: 0;
  margin: 1rem 0 0 0;
}

.post-item {
  margin: 1.5rem 0;
  padding: 0.5rem 0;
  border-bottom: 1px solid #e8e8e8;
}

.post-item:last-child {
  border-bottom: none;
}

.post-title a {
  color: #333;
  text-decoration: none;
  font-weight: 500;
  font-size: 1.2rem;
}

.post-title a:hover {
  color: #2a7ae2;
}

.post-meta {
  font-size: 0.9rem;
  color: #666;
  margin-top: 0.25rem;
}

.post-excerpt {
  margin-top: 0.5rem;
}
</style>