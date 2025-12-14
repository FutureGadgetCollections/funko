---
title: "Netflix LEGO"
layout: archive
permalink: /collections/netflix/
taxonomy: netflix
author_profile: false
---

All blog posts about Netflix LEGO sets.

{% assign netflix_posts = site.categories.netflix %}
{% for post in netflix_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if netflix_posts.size == 0 %}
*No Netflix LEGO blog posts yet.*
{% endif %}
