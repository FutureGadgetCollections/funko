---
permalink: /guide/
title: "Guide"
layout: archive
author_profile: false
---

A collection of guides and tutorials for LEGO collecting and building.

## Guides

{% assign guide_posts = site.categories.guide %}
{% if guide_posts.size > 0 %}
  {% for post in guide_posts reversed %}
    {% include archive-single.html %}
  {% endfor %}
{% else %}
  *No guide entries yet.*
{% endif %}
