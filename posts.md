---
title: "Blog"
layout: default
permalink: /posts
redirect_from:
  - /posts.html
---

Welcome to my stream of consciousness.  Expect nothing amazing and you won't be
disappointed.

{% if site.posts.size > 0 %}
{% for post in site.posts %}
{{ post.date | date: site.date_format }}
  : [{{ post.title | escape }}]({{ post.url | relative_url }})
{% endfor %}
{% endif %}
