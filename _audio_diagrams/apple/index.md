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

---
## All Apple devices
The copy button doesn't work you will need to highlight and copy.
  <div class="language-xml highlighter-rouge"><div class="code-header">
    <span data-label-text="XML"><i class="fas fa-code small"></i></span>
  <button aria-label="copy" data-title-succeed="Copied!" data-original-title="" title=""><i class="far fa-clipboard"></i></button></div><div class="highlight"><code><table class="rouge-table"><tbody><tr><td class="rouge-gutter gl"></td><td class="rouge-code">
    <pre>&lt;mxlibrary&gt;[
{% assign sorted-diagrams = site.audio_diagrams | where: "manufacturer","Apple" %}{% for audio_diagram in sorted-diagrams %}{% if audio_diagram.class != "Manufacturer" %}{{ audio_diagram.xml }}{% unless forloop.last %},
{% endunless -%}{% endif %}{% endfor %}
]&lt;/mxlibrary&gt;
</pre></td></tr></tbody></table></code></div></div>