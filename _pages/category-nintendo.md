---
title: "Nintendo LEGO"
layout: archive
permalink: /collections/nintendo/
taxonomy: nintendo
author_profile: false
---

All blog posts about Nintendo LEGO sets.

{% assign nintendo_posts = site.categories.nintendo %}
{% for post in nintendo_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if nintendo_posts.size == 0 %}
*No Nintendo LEGO blog posts yet.*
{% endif %}
