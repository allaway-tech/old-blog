---
layout: default # You can ommit this if you've set it as a default
title: Audio Diagrams
picture: https://media.allaway.tech/blog/media/audio_diagrams/apple/apple_logo.jpeg
description: The Apple mac mini
order: 1
---

<br />
<br />
# Audio Diagrams
{% assign sorted-diagrams = site.audio_diagrams | where: "class","Manufacturer" %}
{% for audio_diagram in sorted-diagrams %}
  <hr />
  <h2>
    <a href="{{ audio_diagram.url }}">
      <img src="{{ audio_diagram.picture }}" alt="Preview of {{ audio_diagram.title | downcase }} system block diagram"> {{ audio_diagram.title }}
    </a>
  </h2>
{% endfor %}