---
layout: default
title: photos
---

<h2>photos</h2>

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
    body { 
      font-family: "IBM Plex Mono", ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
      max-width: 900px;
      margin: 2rem auto;
      line-height: 1.6;
      background-color: #fff;
      color: #111;
    }
    a { color: blue; text-decoration: underline; }
    nav ul { list-style: disc; padding-left: 1.25rem; }
    header h1 { margin-bottom: 0.5rem; font-weight: 500; }
    img { max-width: 100%; height: auto; }
    figure { margin: 0 0 2rem 0; }
    figcaption { font-size: 0.9em; opacity: 0.7; }
</style>