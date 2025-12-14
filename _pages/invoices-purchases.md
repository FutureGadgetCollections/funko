---
title: "Purchases"
layout: archive
permalink: /invoices/purchases/
author_profile: false
---

All purchase transactions for the LEGO collection.

{% assign purchase_posts = site.categories.purchases %}
{% if purchase_posts.size > 0 %}
  {% assign posts_by_year = purchase_posts | group_by_exp: "post", "post.date | date: '%Y'" %}
  {% for year in posts_by_year %}

## {{ year.name }}

{% for post in year.items reversed %}
      {% include archive-single.html %}
    {% endfor %}
  {% endfor %}
{% else %}
  *No purchase records yet.*
{% endif %}
