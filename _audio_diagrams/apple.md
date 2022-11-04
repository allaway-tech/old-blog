---
layout: default # You can ommit this if you've set it as a default
title: Apple
class: Computer
picture: /media/audio_diagrams/apple/apple_mac_mini.jpg
description: The Apple mac mini
---

{% for audio_diagram in site.audio_diagrams %}
  <h2>
    <a href="{{ audio_diagram.url }}">
      {{ audio_diagram.title }} - {{ audio_diagram.class }}
    </a>
  </h2>
  <img src="{{ page.picture }}" alt="Preview of {{ page.title | downcase }} system block diagram">
{% endfor %}
