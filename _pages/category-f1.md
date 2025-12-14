---
title: "F1 LEGO"
layout: archive
permalink: /collections/f1/
taxonomy: f1
author_profile: false
---

All blog posts about F1 LEGO sets.

{% assign f1_posts = site.categories.f1 %}
{% for post in f1_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if f1_posts.size == 0 %}
*No F1 LEGO blog posts yet.*
{% endif %}
