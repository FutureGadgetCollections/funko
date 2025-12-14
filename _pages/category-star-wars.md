---
title: "Star Wars LEGO"
layout: archive
permalink: /collections/star-wars/
taxonomy: star-wars
author_profile: false
---

All blog posts about Star Wars LEGO sets.

{% assign star_wars_posts = site.categories.star-wars %}
{% for post in star_wars_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if star_wars_posts.size == 0 %}
*No Star Wars LEGO blog posts yet.*
{% endif %}
