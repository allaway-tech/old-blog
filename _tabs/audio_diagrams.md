---
layout: default # You can ommit this if you've set it as a default
title: Audio Diagrams
picture: https://media.allaway.tech/blog/media/audio_diagrams/apple/apple_logo.jpeg
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

---
## All devices
  <div class="language-xml highlighter-rouge"><div class="code-header">
    <span data-label-text="XML"><i class="fas fa-code small"></i></span>
  <button aria-label="copy" data-title-succeed="Copied!" data-original-title="" title=""><i class="far fa-clipboard"></i></button></div><div class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"></td><td class="rouge-code">
    <pre>&lt;mxlibrary&gt;[
{% assign sorted-diagrams = site.audio_diagrams %}{% for audio_diagram in sorted-diagrams %}{% if audio_diagram.class != "Manufacturer" %}{{ audio_diagram.xml }}{% unless forloop.last %},
{% endunless -%}{% endif %}{% endfor %}
]&lt;/mxlibrary&gt;
</pre></td></tr></tbody></table></code></div></div>