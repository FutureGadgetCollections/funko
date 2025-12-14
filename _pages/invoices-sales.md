---
title: "Sales"
layout: archive
permalink: /invoices/sales/
author_profile: false
---

All sale transactions for the LEGO collection.

{% assign sale_posts = site.categories.sales %}
{% if sale_posts.size > 0 %}
  {% assign posts_by_year = sale_posts | group_by_exp: "post", "post.date | date: '%Y'" %}
  {% for year in posts_by_year %}

## {{ year.name }}

{% for post in year.items reversed %}
      {% include archive-single.html %}
    {% endfor %}
  {% endfor %}
{% else %}
  *No sale records yet.*
{% endif %}
