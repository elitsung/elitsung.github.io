---
layout: default
title: photos
banner_image: /assets/banner.jpg
---

<!-- 1) Emit the list of photo files from Jekyll -->
<script>
  const PHOTO_FILES = [
  {% assign imgs = site.static_files | where_exp: "f", "f.path contains 'assets/photos/'" %}
  {% for f in imgs %}
    { src: "{{ f.path | relative_url }}", name: "{{ f.name | escape }}" }{% unless forloop.last %},{% endunless %}
  {% endfor %}
  ];
</script>

<!-- 2) Viewer + controls -->
<div id="photo-viewer" style="text-align:center">
  <div id="photo-title" style="margin-bottom:.4rem;font-weight:500;"></div>
  <a id="photo-link" href="#" target="_blank" rel="noopener">
    <img id="photo-img" src="" alt="" loading="lazy" decoding="async" style="max-width:66%;height:auto;">
  </a>
  <figcaption id="photo-caption" style="margin-top:.4rem;opacity:.75;"></figcaption>

  <div id="controls" style="margin-top:1rem;user-select:none;">
    <button id="prevBtn" aria-label="Previous">⟵ Prev</button>
    <button id="randBtn" aria-label="Random">Random</button>
    <button id="nextBtn" aria-label="Next">Next ⟶</button>
  </div>
</div>

<!-- 3) Tiny EXIF reader (client-side) -->
<script src="https://unpkg.com/exifr@7.1.3/dist/full.umd.js"></script>
<script>
(async function () {
  // helpers
  const fmt = d => d ? d.toLocaleDateString(undefined, {year:'numeric', month:'long', day:'numeric'}) : '';
  const byFilenameGuess = name => {
    // Try to guess a date from filename like 2024-05-17, 20240517, 24_05_17, etc.
    const s = name.toLowerCase();
    let m = s.match(/(20\d{2})[-_\.]?(0[1-9]|1[0-2])[-_\.]?([0-3]\d)/);
    if (m) return new Date(`${m[1]}-${m[2]}-${m[3]}T12:00:00`);
    return null;
  };

  // 1) Load EXIF for each photo, build list
  const items = [];
  for (const f of PHOTO_FILES) {
    try {
      const exif = await exifr.parse(f.src);
      const dt = exif?.DateTimeOriginal || exif?.CreateDate || exif?.ModifyDate;
      const date = dt ? new Date(dt) : byFilenameGuess(f.name);
      items.push({ src: f.src, name: f.name, date });
    } catch (e) {
      items.push({ src: f.src, name: f.name, date: byFilenameGuess(f.name) });
    }
  }

  // 2) Sort by date (oldest -> newest). Null dates go last, sorted by name.
  items.sort((a, b) => {
    if (a.date && b.date) return a.date - b.date;
    if (a.date) return -1;
    if (b.date) return 1;
    return a.name.localeCompare(b.name);
  });

  // 3) State via URL (?i=)
  const params = new URLSearchParams(location.search);
  let idx = Math.min(Math.max(parseInt(params.get('i') || '0', 10), 0), Math.max(items.length - 1, 0));

  // 4) Render
  const img = document.getElementById('photo-img');
  const link = document.getElementById('photo-link');
  const title = document.getElementById('photo-title');
  const cap = document.getElementById('photo-caption');

  function render() {
    if (!items.length) {
      title.textContent = '';
      img.removeAttribute('src');
      img.alt = '';
      link.removeAttribute('href');
      cap.innerHTML = '<em>No photos found.</em>';
      return;
    }
    const it = items[idx];
    img.src = it.src;
    img.alt = it.name;
    link.href = it.src;
    let dateText = it.date ? fmt(it.date) : '';
    cap.innerHTML = `
      <div class="photo-date">${dateText}</div>
      <div class="photo-name">${it.name}</div>
    `;
    const url = new URL(location.href);
    url.searchParams.set('i', idx);
    history.replaceState(null, '', url);
  }

  function next() {
    if (!items.length) return;
    idx = (idx + 1) % items.length;
    render();
  }
  function prev() {
    if (!items.length) return;
    idx = (idx - 1 + items.length) % items.length;
    render();
  }
  function rand() {
    if (items.length <= 1) return;
    let r;
    do { r = Math.floor(Math.random() * items.length); } while (r === idx);
    idx = r; render();
  }

  // 5) Wire up controls & keyboard
  document.getElementById('nextBtn').onclick = next;
  document.getElementById('prevBtn').onclick = prev;
  document.getElementById('randBtn').onclick = rand;
  document.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowRight') next();
    if (e.key === 'ArrowLeft') prev();
    if (e.key.toLowerCase() === 'r') rand();
  });

  render();
})();
</script>

<!-- 4) Minimal button styling to match your vibe -->
<style>
  #controls button {
    font-family: inherit;
    font-size: 0.95rem;
    background: transparent;
    border: 1px solid #000;
    padding: 0.25rem 0.6rem;
    margin: 0 0.25rem;
    cursor: pointer;
  }
  #controls button:hover {
    font-style: italic;
    background: #f5f5f5;
  }
</style>