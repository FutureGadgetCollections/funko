---
title: "Harry Potter LEGO"
layout: archive
permalink: /collections/harry-potter/
taxonomy: harry-potter
author_profile: false
---

All blog posts about Harry Potter LEGO sets.

{% assign harry_potter_posts = site.categories.harry-potter %}
{% for post in harry_potter_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if harry_potter_posts.size == 0 %}
*No Harry Potter LEGO blog posts yet.*
{% endif %}
