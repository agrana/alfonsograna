---
layout: page
title: Home Hermes Setup
permalink: /home-hermes-setup/
---

<style>
.diagram-page {
  max-width: 1120px;
}
.diagram-frame {
  background: #fff;
  border: 1px solid rgba(255,255,255,0.18);
  border-radius: 12px;
  padding: 1rem;
  overflow-x: auto;
  box-shadow: 0 18px 48px rgba(0,0,0,0.24);
}
.diagram-frame img {
  display: block;
  width: 100%;
  min-width: 760px;
  height: auto;
}
.diagram-actions {
  display: flex;
  gap: .75rem;
  flex-wrap: wrap;
  margin: 1rem 0 1.5rem;
}
.diagram-actions a {
  display: inline-block;
  padding: .45rem .7rem;
  border: 1px solid currentColor;
  border-radius: 8px;
}
</style>

<div class="diagram-page">

A map of the current Hermes-at-home setup: WhatsApp as the conversation entry point, Hermes running on the Fedora mini PC, and Codex/Cursor tools connected for inference and coding work.

<div class="diagram-actions">
  <a href="{{ '/assets/diagrams/home-hermes-setup.svg' | relative_url }}">Open SVG</a>
  <a href="{{ '/assets/diagrams/home-hermes-setup.excalidraw' | relative_url }}">Download Excalidraw source</a>
</div>

<div class="diagram-frame">
  <img src="{{ '/assets/diagrams/home-hermes-setup.svg' | relative_url }}" alt="Home Hermes setup diagram">
</div>

</div>
