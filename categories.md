---
layout: page
title: Categories
permalink: /categories/
---

<div class="categories-overview">
  {% assign sorted_categories = site.categories | sort %}
  
  <div class="category-grid">
    {% for category in sorted_categories %}
      <div class="category-card">
        <h2 id="{{ category[0] | slugify }}">{{ category[0] }}</h2>
        <span class="post-count">{{ category[1].size }} {% if category[1].size == 1 %}post{% else %}posts{% endif %}</span>
        <ul class="post-list">
          {% assign sorted_posts = category[1] | sort: 'date' | reverse %}
          {% for post in sorted_posts %}
            <li class="post-item">
              <div class="post-title">
                <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
              </div>
              <div class="post-meta">
                <span class="post-date">{{ post.date | date: "%b %-d, %Y" }}</span>
              </div>
            </li>
          {% endfor %}
        </ul>
      </div>
    {% endfor %}
  </div>
</div>

<style>
.categories-overview {
  margin: 2rem 0;
}

.category-grid {
  display: grid;
  gap: 2rem;
}

.category-card {
  background: #f8f9fa;
  border-radius: 8px;
  padding: 1.5rem;
  margin-bottom: 1rem;
}

.category-card h2 {
  margin: 0;
  color: #2a7ae2;
  border-bottom: 2px solid #e8e8e8;
  padding-bottom: 0.5rem;
}

.post-count {
  font-size: 0.9rem;
  color: #666;
  margin-top: 0.5rem;
  display: inline-block;
}

.post-list {
  list-style: none;
  padding: 0;
  margin: 1rem 0 0 0;
}

.post-item {
  margin: 1rem 0;
  padding: 0.5rem;
  border-bottom: 1px solid #e8e8e8;
}

.post-item:last-child {
  border-bottom: none;
}

.post-title a {
  color: #333;
  text-decoration: none;
  font-weight: 500;
}

.post-title a:hover {
  color: #2a7ae2;
}

.post-meta {
  font-size: 0.9rem;
  color: #666;
  margin-top: 0.25rem;
}

@media (min-width: 768px) {
  .category-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
</style>
