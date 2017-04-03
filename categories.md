---
layout: page
title:  Categories
permalink: /categories/
---
<div class="tag-list">
{% for cat in site.categories %}
  <div class="tag-group">
    {% capture cat_name %}{{ cat | first }}{% endcapture %}
    <p></p>
    <h3 class="tag-group-title" id="{{ cat_name | slugize }}">{{ cat_name }}</h3>
    {% for post in site.categories[cat_name] %}
    <article class="tag-item">
      <a class="tag-item-title" href="{{ site.url }}{{ post.url }}">{{post.title}}</a>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>