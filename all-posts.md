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

<style>
.all-posts {
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

.post-categories a {
  color: #2a7ae2;
  text-decoration: none;
}

.post-categories a:hover {
  text-decoration: underline;
}

.post-excerpt {
  margin-top: 0.5rem;
}
</style>