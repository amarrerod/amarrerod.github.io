---
layout: page
permalink: /publications/
title: publications
description: Publications by categories in reversed chronological order
first_year: 2018
last_year: 2024
nav: true
---
<!-- _pages/publications.md -->
<div class="publications">

{%- for y in (page.first_year..page.last_year) reversed %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}

</div>
