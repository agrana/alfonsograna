---
layout: page
title: Home Hermes Setup
permalink: /home-hermes-setup/
---

<style>
.diagram-page {
  max-width: 1180px;
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
  min-width: 900px;
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

<h2>Hermes usage snapshot</h2>

Agent usage on OpenRouter.

<div class="diagram-frame">
  <img src="{{ '/assets/images/hermes-agent-token-usage.png' | relative_url }}" alt="Hermes Agent leading a coding assistant token usage dashboard">
</div>


<h2>How this setup works</h2>

This is not just a chatbot running on a box. The useful mental model is: Hermes is a small agent runtime at the center of my home AI layer. WhatsApp, SSH and remote desktop are just the doors into it.

When I send a WhatsApp message, the Gateway receives the message, finds the right conversation history, rebuilds the context, and hands it to the Hermes agent loop. The loop is simple but powerful: build context, call the model, let the model use tools, feed tool results back, and finish with a response. If something should be remembered, it can be written back into memory.

The context is where the system becomes personal. Hermes combines the current conversation with its personality, my user profile, durable memory, available skills, tool descriptions, and relevant past session context. In this setup, Honcho handles richer long-term memory, Obsidian remains the human-readable knowledge base, and local session history keeps day-to-day continuity.

Cron jobs make the box feel alive even when I am not actively chatting. Hermes runs its own scheduler loop, stored under <code>~/.hermes/cron/jobs.json</code>, and writes run reports under <code>~/.hermes/cron/output/</code>. That is what powers background workflows like Gmail triage or reminders: small scripts or agent prompts that wake up on a schedule, do useful work, and stay quiet when there is nothing to report.

The practical split is intentional:

<ul>
  <li><strong>WhatsApp</strong> is the low-friction control surface.</li>
  <li><strong>Fedora mini PC</strong> is the always-on execution environment.</li>
  <li><strong>Hermes Gateway</strong> turns incoming messages into agent sessions.</li>
  <li><strong>Hermes Agent</strong> reasons, calls tools, edits files, runs commands and coordinates workflows.</li>
  <li><strong>Honcho + Obsidian</strong> give the system memory: one agent-native, one human-native.</li>
  <li><strong>External services</strong> stay modular: inference providers, coding agents, GitHub, Gmail, Hue lights and other APIs can be swapped without changing the core setup.</li>
</ul>

The point of the architecture is not to make a perfect diagram. It is to make the home machine behave like a personal operating layer: reachable from chat, capable of acting on the local system, aware of my preferences, and able to keep background routines running.

<div class="diagram-actions">
  <a href="{{ '/assets/diagrams/home-hermes-setup.svg' | relative_url }}">Open SVG</a>
  <a href="{{ '/assets/diagrams/home-hermes-setup.excalidraw' | relative_url }}">Download Excalidraw source</a>
</div>

<div class="diagram-frame">
  <img src="{{ '/assets/diagrams/home-hermes-setup.svg' | relative_url }}" alt="Home AI operating layer diagram">
</div>

</div>

