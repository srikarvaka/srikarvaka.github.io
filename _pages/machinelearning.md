---
title: "Tags"
permalink: /machine-learning/
header:
  overlay_image: "/images/code.png"
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
---



{% include base_path %}
{% include group-by-array collection=site.posts field="tags" %}

{% for tag in group_names %}
  {% assign posts = group_items[forloop.index0] %}
  <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2>
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}
