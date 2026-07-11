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
  box-shadow: 0 18px 48px rgba(0,0,0,0.24);
}
.diagram-frame img {
  display: block;
  max-width: 100%;
  width: 100%;
  height: auto;
}
.usage-table {
  width: 100%;
  margin: 1rem 0 .5rem;
  border-collapse: collapse;
}
.usage-table th,
.usage-table td {
  padding: .65rem;
  text-align: left;
  border-bottom: 1px solid rgba(255,255,255,0.18);
}
.usage-table th:first-child,
.usage-table td:first-child {
  width: 4rem;
  text-align: center;
}
.source-note {
  margin-bottom: 1.5rem;
  font-size: .9rem;
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

<h1>Home Hermes Setup</h1>

<p>There is a reason Hermes is the top agent on OpenRouter: it is designed to keep working beyond a single chat. It runs persistently, carries memory across sessions, builds reusable skills, calls tools, and handles scheduled work. That combination makes it useful as a personal operating layer rather than only a coding assistant.</p>

<h2>Hermes on OpenRouter</h2>

<p>OpenRouter currently ranks Hermes first among coding agents by token volume. This measures public traffic routed through OpenRouter, not the total usage of each agent across every provider, so tools that mainly use their own APIs are undercounted.</p>

<div class="diagram-frame">
  <img src="{{ '/assets/images/hermes-agent-token-usage.png' | relative_url }}" alt="Hermes Agent leading a coding assistant token usage dashboard">
</div>

<h3>Recent results</h3>

<table class="usage-table">
  <thead>
    <tr>
      <th>Rank</th>
      <th>Coding agent</th>
      <th>Tokens today</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1</td><td>Hermes Agent</td><td>995B</td></tr>
    <tr><td>2</td><td>Kilo Code</td><td>262B</td></tr>
    <tr><td>3</td><td>Claude Code</td><td>196B</td></tr>
    <tr><td>4</td><td>OpenClaw</td><td>165B</td></tr>
    <tr><td>5</td><td>Cline</td><td>102B</td></tr>
  </tbody>
</table>

<p class="source-note">Source: <a href="https://openrouter.ai/apps/category/coding">OpenRouter Coding Agents Rankings</a>, captured July 12, 2026. OpenRouter also provides an authenticated <a href="https://openrouter.ai/docs/cookbook/administration/data-api">Data API</a> for app rankings and daily model totals.</p>

<h2>How this setup works</h2>

<p>Hermes is the agent runtime at the center of my home AI setup. It runs continuously on a Fedora PC and connects conversations, tools, memory, scheduled jobs, and external services.</p>

<h3>WhatsApp is the front door</h3>

<p>I control Hermes mostly through a private WhatsApp conversation with myself, monitored by the Hermes Gateway. I already used that conversation to save interesting things I found. With Hermes watching it, those messages can be indexed in my knowledge base and retrieved as context later.</p>

<h3>The agent loop</h3>

<ol>
  <li>Build context for the request.</li>
  <li>Call the selected model.</li>
  <li>Let the model choose and use tools.</li>
  <li>Feed the tool results back into the model.</li>
  <li>Return the final response.</li>
</ol>

<h3>Context and memory</h3>

<p>Context is where personalization happens. Hermes combines the current conversation with its personality, user profile, durable memory, available skills, tool descriptions, and relevant past sessions. Honcho provides richer long-term memory, Obsidian remains the human-readable knowledge base, and local session history maintains day-to-day continuity.</p>

<h3>Scheduled work</h3>

<p>Hermes runs its own scheduler loop. Jobs are stored in <code>~/.hermes/cron/jobs.json</code>, and run reports are written to <code>~/.hermes/cron/output/</code>. This lets the same agent handle both conversations and unattended recurring work.</p>

<h3>Components</h3>

<dl>
  <dt><strong>WhatsApp</strong></dt>
  <dd>The messaging interface. Hermes only replies to my own messages.</dd>
  <dt><strong>Fedora PC</strong></dt>
  <dd>The always-on execution environment.</dd>
  <dt><strong>Hermes Gateway</strong></dt>
  <dd>Turns incoming messages into agent sessions.</dd>
  <dt><strong>Hermes Agent</strong></dt>
  <dd>Reasons, calls tools, edits files, runs commands, and coordinates workflows.</dd>
  <dt><strong>Honcho and Obsidian</strong></dt>
  <dd>Provide two complementary memory layers: one agent-native and one human-native.</dd>
  <dt><strong>External services</strong></dt>
  <dd>Inference providers, coding agents, GitHub, Gmail, Hue lights, and other APIs remain modular and can be swapped without changing the core setup.</dd>
</dl>

<h3>Architecture</h3>

<div class="diagram-actions">
  <a href="{{ '/assets/diagrams/home-hermes-setup.svg' | relative_url }}">Open SVG</a>
  <a href="{{ '/assets/diagrams/home-hermes-setup.excalidraw' | relative_url }}">Download Excalidraw source</a>
</div>

<div class="diagram-frame">
  <img src="{{ '/assets/diagrams/home-hermes-setup.svg' | relative_url }}" alt="Home AI operating layer diagram">
</div>

</div>
