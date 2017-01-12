---
title: About
permalink: /about-me/

---
![Berlin wall graffiti]({{ site.url }}/assets/images/berlin_wall.png)

Listen, to everything. And think thereafter.

Leverage the power of diffuse thinking mode by studying several different topics and let associations improve the present and create ideas for the future.

{% for post in site.about-me %}

{% include archive-single.html type="grid" %}

{% endfor %}
