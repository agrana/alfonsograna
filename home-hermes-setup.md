---
layout: page
title: My Hermes Setup
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
.diagram-expand {
  display: block;
  width: 100%;
  padding: 0;
  border: 0;
  background: transparent;
  cursor: zoom-in;
}
.diagram-dialog {
  box-sizing: border-box;
  width: 100vw;
  max-width: 100vw;
  height: 100vh;
  max-height: 100vh;
  margin: 0;
  padding: 1rem;
  border: 0;
  background: #fff;
}
.diagram-dialog::backdrop {
  background: rgba(0,0,0,0.88);
}
.diagram-dialog img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: contain;
}
.diagram-dialog-close {
  position: fixed;
  top: 1rem;
  right: 1rem;
  z-index: 1;
  padding: .5rem .75rem;
  border: 1px solid #111827;
  border-radius: 8px;
  background: #fff;
  color: #111827;
  cursor: pointer;
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
.diagram-page code {
  font-size: .85em;
}
</style>

<div class="diagram-page">

<h1>My Hermes Setup</h1>

<p>There is a reason <a href="https://hermes-agent.nousresearch.com/">Hermes</a> is the top agent on <a href="https://openrouter.ai/">OpenRouter</a>: it is designed to keep working beyond a single chat. It runs persistently, carries memory across sessions, builds reusable skills, calls tools, and handles scheduled work. That combination makes it useful as a personal assistant coordinating work for other agents. OpenRouter currently ranks Hermes first among coding agents by token volume. This measures public traffic routed through OpenRouter, not the total usage of each agent across every provider, so tools that mainly use their own APIs are undercounted.</p>

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
    <tr><td>1</td><td><a href="https://openrouter.ai/apps/hermes-agent">Hermes Agent</a></td><td>995B</td></tr>
    <tr><td>2</td><td><a href="https://openrouter.ai/apps/kilo-code">Kilo Code</a></td><td>262B</td></tr>
    <tr><td>3</td><td><a href="https://openrouter.ai/apps/claude-code">Claude Code</a></td><td>196B</td></tr>
    <tr><td>4</td><td><a href="https://openrouter.ai/apps/openclaw">OpenClaw</a></td><td>165B</td></tr>
    <tr><td>5</td><td><a href="https://openrouter.ai/apps/cline">Cline</a></td><td>102B</td></tr>
  </tbody>
</table>

<p class="source-note">Source: <a href="https://openrouter.ai/apps/category/coding">OpenRouter Coding Agents Rankings</a>, captured July 12, 2026.</p>

<h2>How this setup works</h2>

<p>Hermes is the agent runtime at the center of my AI setup. It runs continuously on a self-hosted box and connects conversations, tools, memory, scheduled jobs, and external services. Can be any always-on machine.</p>

<div class="diagram-frame">
  <button class="diagram-expand" type="button" data-dialog-target="architecture-dialog" aria-label="Expand the home AI operating layer diagram">
    <img src="{{ '/assets/diagrams/home-hermes-setup.svg' | relative_url }}" alt="Home AI operating layer diagram">
  </button>
</div>

<dialog class="diagram-dialog" id="architecture-dialog" aria-label="Expanded home AI operating layer diagram">
  <button class="diagram-dialog-close" type="button">Close</button>
  <img src="{{ '/assets/diagrams/home-hermes-setup.svg' | relative_url }}" alt="Home AI operating layer diagram">
</dialog>

<h3>Components</h3>

<p>The core setup separates agent execution, message delivery, and long-term memory into three distinct layers.</p>

<dl>
  <dt><strong><a href="https://hermes-agent.nousresearch.com/">Hermes Agent</a></strong></dt>
  <dd>Reasons, calls tools, edits files, runs commands, and coordinates workflows.</dd>
  <dt><strong><a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/">Hermes Gateway</a></strong></dt>
  <dd>Turns incoming messages into agent sessions.</dd>
  <dt><strong><a href="https://honcho.dev/">Honcho</a> and <a href="https://obsidian.md/">Obsidian</a></strong></dt>
  <dd>Provide two complementary memory layers: one agent-native and one human-native.</dd>
</dl>

<h3>Hermes Gateway</h3>

<p>I control Hermes mostly through a private WhatsApp conversation with myself, monitored by the <a href="https://hermes-agent.nousresearch.com/docs/user-guide/messaging/">Hermes Gateway</a>. ; The gateway supports more than 20 messaging services, including Telegram, Discord, Slack, Signal, email, SMS, and Microsoft Teams.</p>

<p>I already used this same whtasapp thread to save interesting things I found. With Hermes watching it, those messages can be indexed in my knowledge base and retrieved as context later.</p>

<h3>The agent loop</h3>

<p>Each request moves through a simple loop that adds context, invokes a model, and repeats tool use until Hermes can return a final response.</p>

<div class="diagram-frame">
  <button class="diagram-expand" type="button" data-dialog-target="agent-loop-dialog" aria-label="Expand the Hermes agent loop diagram">
    <img src="{{ '/assets/diagrams/hermes-agent-loop.svg' | relative_url }}" alt="Hermes agent loop diagram">
  </button>
</div>

<dialog class="diagram-dialog" id="agent-loop-dialog" aria-label="Expanded Hermes agent loop diagram">
  <button class="diagram-dialog-close" type="button">Close</button>
  <img src="{{ '/assets/diagrams/hermes-agent-loop.svg' | relative_url }}" alt="Hermes agent loop diagram">
</dialog>

<h3>Context and memory</h3>

<p>Context is where personalization happens. Hermes combines the current conversation with its personality, user profile, durable memory, available skills, tool descriptions, and relevant past sessions. Local session history maintains continuity within day-to-day conversations, while <a href="https://obsidian.md/">Obsidian</a> and <a href="https://honcho.dev/">Honcho</a> cover two different kinds of long-term context.</p>

<h4><a href="https://obsidian.md/">Obsidian</a>: External memory in MD format easy to read and edit a knowledge base that Hermes Agent knows it can grab at any time</h4>

<p>Obsidian holds the material I want to read, organize, and edit myself: saved links, research, project notes, summaries, and connections between ideas. Because the vault is made of Markdown files, Hermes can search and update it with normal file tools while the knowledge remains portable and easy to audit. Is basically a folder structure with mark-up files inside obsidian is just the interfase that can be anything.</p>

<h4><a href="https://honcho.dev/">Honcho</a>: agent-native memory</h4>

<p>Honcho gives Hermes persistent memory across sessions. It builds a model of my preferences, goals, communication style, and recurring patterns from previous conversations. Hermes can retrieve relevant conclusions through semantic search and inject session context when responding, so useful personal context does not have to be restated in every conversation. I am still learning how honcho works It seems a bit magicall still. Honcho is self-hosted for me.</p>

<h3>Scheduled work</h3>

<p><a href="https://hermes-agent.nousresearch.com/docs/user-guide/features/cron">Hermes cron jobs</a> turn a prompt into unattended recurring work. A job can use a standard cron expression or a natural-language schedule and can include specific skills and a delivery target.</p>

<p>The scheduler is part of the Hermes Gateway. While the gateway is running, it checks for due jobs every 60 seconds. Each due job starts in a fresh agent session, loads any attached skills, runs its prompt to completion, and delivers the final response to its configured chat or local output. Hermes then records the result and calculates the next run time.</p>

<p>Job definitions are stored in <code>~/.hermes/cron/jobs.json</code>. Reports from individual runs are saved under <code>~/.hermes/cron/output/{job_id}/{timestamp}.md</code>. This lets the same agent handle both conversations and unattended recurring work without carrying unrelated conversation history into a scheduled run.</p>

</div>

<script>
  document.querySelectorAll('[data-dialog-target]').forEach((diagramExpandButton) => {
    const diagramDialog = document.getElementById(diagramExpandButton.dataset.dialogTarget);
    const diagramCloseButton = diagramDialog.querySelector('.diagram-dialog-close');

    diagramExpandButton.addEventListener('click', () => diagramDialog.showModal());
    diagramCloseButton.addEventListener('click', () => diagramDialog.close());
    diagramDialog.addEventListener('click', (event) => {
      if (event.target === diagramDialog) {
        diagramDialog.close();
      }
    });
  });
</script>
