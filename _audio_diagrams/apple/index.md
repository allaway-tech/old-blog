---
layout: default # You can ommit this if you've set it as a default
title: Apple
manufacturer: Apple
class: Manufacturer
picture: https://media.allaway.tech/blog/media/audio_diagrams/apple/apple_logo.png # 200 x 110
---

<br />
<br />
# Apple products
{% assign sorted-diagrams = site.audio_diagrams | where: "manufacturer","Apple" %}
{% for audio_diagram in sorted-diagrams %}
  {% if audio_diagram.class != "Manufacturer" %}
  <hr />
  <h2>
    <a href="{{ audio_diagram.url }}">
      {{ audio_diagram.title }} - {{ audio_diagram.class }}
    </a>
  </h2>
  <img src="{{ audio_diagram.picture }}" alt="Preview of {{ audio_diagram.title | downcase }} system block diagram">
  {% endif %}
{% endfor %}