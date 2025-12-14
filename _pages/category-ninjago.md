---
title: "Ninjago LEGO"
layout: archive
permalink: /collections/ninjago/
taxonomy: ninjago
author_profile: false
---

All blog posts about Ninjago LEGO sets.

{% assign ninjago_posts = site.categories.ninjago %}
{% for post in ninjago_posts %}
  {% include archive-single.html %}
{% endfor %}

{% if ninjago_posts.size == 0 %}
*No Ninjago LEGO blog posts yet.*
{% endif %}
