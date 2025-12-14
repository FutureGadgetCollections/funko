---
permalink: /portfolio-summary/
title: "Portfolio Summary"
layout: archive
author_profile: false
---

This section showcases portfolio summary entries and analysis.

## Portfolio Entries

{% assign portfolio_posts = site.categories.portfolio-summary %}
{% if portfolio_posts.size > 0 %}
  {% for post in portfolio_posts reversed %}
    {% include archive-single.html %}
  {% endfor %}
{% else %}
  *No portfolio summary entries yet.*
{% endif %}
