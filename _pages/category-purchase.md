---
title: "Purchase Invoices"
layout: archive
permalink: /categories/purchase/
taxonomy: purchase
author_profile: true
---

All items purchased for the collection are documented here.

{% assign purchase_posts = site.categories.purchase %}
{% for post in purchase_posts %}
  {% include archive-single.html %}
{% endfor %}
