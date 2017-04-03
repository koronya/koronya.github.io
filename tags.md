---
layout: page
title:  Tags
permalink: /tags/
---

<h2>Tag cloud <i class="fa fa-cloud info-text-color"></i></h2>
----
<ul class="tag-cloud">
{% for tag in site.tags %}
  <li style="font-size: {{ tag | last | size | times: 100 | divided_by: site.tags.size | plus: 70  }}%">
    <a class="post-tag" href="#{{ tag | first | slugize }}">
      {{ tag | first }}
    </a>
  </li>
{% endfor %}
</ul>

<h2>Tag List</h2>
----

<div class="tag-list">
{% for tag in site.tags %}
  <div class="tag-group">
    <p></p>
    {% capture tag_name %}{{ tag | first }}{% endcapture %}
    <h3 class="tag-group-title" id="{{ tag_name | slugize }}">{{ tag_name }}</h3>
    {% for post in site.tags[tag_name] %}
    <article class="tag-item">
      <a class="tag-item-title" href="{{ site.url }}{{ post.url }}">{{post.title}}</a>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>
