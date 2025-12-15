---
title: "Marvel Funko"
layout: archive
permalink: /collection/marvel/
taxonomy: marvel
author_profile: false
---

All blog posts about Marvel Funko Pops.

{% assign marvel_posts = site.categories.marvel %}
{% for post in marvel_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if marvel_posts.size == 0 %}
*No Marvel Funko blog posts yet.*
{% endif %}
