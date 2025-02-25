---
layout: page
title: Categories
permalink: /categories/
---

<div class="category-buttons">
  <a href="/categories/python" class="category-button python">Python</a>
  <a href="/categories/ai" class="category-button ai">AI</a>
  <a href="/categories/technology" class="category-button technology">Technology</a>
  <a href="/categories/tutorial" class="category-button tutorial">Tutorial</a>
</div>

## All Categories

{% for category in site.categories %}
  <h3>{{ category[0] }}</h3>
  <ul>
    {% for post in category[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: "%B %d, %Y" }}</li>
    {% endfor %}
  </ul>
{% endfor %}