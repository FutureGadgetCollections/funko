---
title: "LEGO Blog Posts"
layout: archive
permalink: /blog/legos/
taxonomy: legos
author_profile: false
---

All blog posts about the LEGO collection.

{% assign legos_posts = site.categories.legos %}
{% for post in legos_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if legos_posts.size == 0 %}
*No LEGO blog posts yet.*
{% endif %}
