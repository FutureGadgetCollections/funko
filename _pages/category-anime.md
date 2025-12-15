---
title: "Anime Funko"
layout: archive
permalink: /collection/anime/
taxonomy: anime
author_profile: false
---

All blog posts about Anime Funko Pops.

{% assign anime_posts = site.categories.anime %}
{% for post in anime_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if anime_posts.size == 0 %}
*No Anime Funko blog posts yet.*
{% endif %}
