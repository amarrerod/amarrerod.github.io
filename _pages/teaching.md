---
layout: page
permalink: /teaching/
title: Teaching
description: Courses that I have taught
nav: true
nav_order: 2
horizontal: false
---

<!-- _pages/teaching.md -->
<div class="courses" markdown="1">
{% assign courses = site.courses %}
  <ul>
  {% for course in courses %}
    <li>
    <h2> {{ course.description }} ({{ course.title }}) </h2> 
     <b>{{ course.degree}} -  {{ course.dates }} @ {{ course.college }} </b>
     <div>
        <p> {{ course.content }} </p>
     </div>
    </li>
  {% endfor %}
</ul>
</div>

