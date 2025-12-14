---
title: "Sale Invoices"
layout: archive
permalink: /categories/sale/
taxonomy: sale
author_profile: true
---

All items sold from the collection are documented here.

{% assign sale_posts = site.categories.sale %}
{% for post in sale_posts %}
  {% include archive-single.html %}
{% endfor %}
