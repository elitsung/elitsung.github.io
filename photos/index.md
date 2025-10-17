---
layout: default
title: photos
---

<h2>Photos</h2>
<p>This page lists every image in <code>assets/photos/</code>. Drop files there and rebuild.</p>

<div class="thumbs">
  {% assign imgs = site.static_files | where_exp: "f", "f.path contains '/assets/photos/'" %}
  {% if imgs.size == 0 %}
    <p><em>No images yet.</em></p>
  {% endif %}
  {% for f in imgs %}
    <figure>
      <a href="{{ f.path }}"><img src="{{ f.path }}" alt="{{ f.name }}" /></a>
      <figcaption>{{ f.name }}</figcaption>
    </figure>
  {% endfor %}
</div>

<style>
  body { font-family: "Times New Roman", Times, serif; max-width: 900px; margin: 2rem auto; line-height: 1.6; }
  img { max-width: 100%; height: auto; margin-bottom: 1rem; }
  figure { margin: 0 0 2rem 0; }
  figcaption { font-size: 0.9em; color: #555; }
</style>