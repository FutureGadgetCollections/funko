---
permalink: /research/
title: "Research"
layout: archive
author_profile: false
---

Research entries and analysis.

## Research Entries

{% assign research_posts = site.categories.research %}
{% if research_posts.size > 0 %}
  {% for post in research_posts reversed %}
    {% include archive-single.html %}
  {% endfor %}
{% else %}
  *No research entries yet.*
{% endif %}
