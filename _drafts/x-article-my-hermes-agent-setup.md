---
layout: post
title: "OpenClaw vs. Hermes: Why Hermes Became My Personal AI Agent"
source_url: "https://alfonsograna.com/my-hermes-agent-setup/"
format: x-article
published: false
author: "Alfonso Grana"
---

# OpenClaw vs. Hermes: Why Hermes Became My Personal AI Agent

*The agent that learns with you.*

I keep repeating myself when I talk to AI.

I explain the same context, restate earlier decisions, and describe again how I like things done. One conversation becomes an isolated event with little coherence between sessions.

I want the AI I talk to this afternoon to feel like the same one I talked to this morning.

That led me to [Hermes Agent](https://hermes-agent.nousresearch.com/). I installed it on a small Fedora Linux mini PC, connected it to WhatsApp, and gave it controlled access to tools, files, scheduled jobs, and long-term memory.

If you are looking at OpenClaw because you want a self-hosted personal agent, Hermes deserves a look too.

## OpenClaw vs. Hermes

[Hermes](https://hermes-agent.nousresearch.com/) and [OpenClaw](https://docs.openclaw.ai/) occupy similar territory. Both can be self-hosted, connect to different model providers, receive messages through multiple channels, and call tools.

Their emphasis differs.

OpenClaw focuses more heavily on its messaging gateway, channel plugins, and connected-device ecosystem. Hermes puts the learning loop closer to the center: persistent memory, reusable skills, skill improvement, scheduled work, and delegation to bounded subagents.

I wanted an agent that could remember useful context and turn a successful procedure into something inspectable and reusable. For that goal, Hermes was the elegant solution.

## The setup

[IMAGE: Architecture diagram — https://alfonsograna.com/assets/diagrams/my-hermes-agent-setup.svg]

The architecture has six parts:

1. **A machine I control.** Hermes runs continuously on my Fedora mini PC.
2. **A familiar front door.** I talk to it through a private WhatsApp conversation.
3. **An agent runtime.** Hermes assembles context, invokes models, calls tools, and checks results.
4. **Human-readable knowledge.** Obsidian stores notes, research, decisions, and project material as Markdown files.
5. **Agent-native memory.** Honcho carries relevant preferences and patterns between conversations.
6. **Reusable work.** Skills and cron jobs turn successful procedures into repeatable or scheduled tasks.

You do not need Fedora or a mini PC. Hermes could run on a Mac mini, a Raspberry Pi, a Kubernetes pod, or a cloud VM.

Choose an environment you understand and can maintain. I use Fedora because I know how to secure it, update it, inspect it, and back it up. I want the agent operating in an environment I control.

## WhatsApp is only the front door

When I send a message, the Hermes Gateway creates an agent session. The session combines the current conversation with relevant memory, available skills, tool descriptions, and selected past context.

The model can then call a tool, inspect the result, and continue until it can return a grounded answer.

[IMAGE: Agent loop diagram — https://alfonsograna.com/assets/diagrams/my-hermes-agent-setup-agent-loop.svg]

This is the difference between a chat interface and an agent runtime.

If I ask about a file, Hermes can read it. If I ask it to run a service, it can execute the command and verify that the service became healthy. If a task should create a document, it can read the document back before claiming success.

WhatsApp lets me reach that system from anywhere without introducing another interface into my day.

## Separate knowledge from personalization

I use two memory layers because they solve different problems.

**Obsidian contains knowledge I want to own explicitly:** project notes, saved links, research, summaries, diagrams, drafts, and decisions. The vault is a folder of Markdown files, so both Hermes and I can search, edit, and audit it with ordinary tools.

**Honcho handles continuity in the working relationship:** preferences, goals, communication style, and recurring patterns that may be relevant in a later conversation.

The distinction is useful:

- Put facts and decisions you must audit in human-readable files.
- Use agent-native memory for personalization and retrieval.
- Treat both as fallible and correct them when necessary.

More memory is not automatically better. Give each worker only the context it needs. A narrow task with bounded access is usually easier to trust than one that can search your entire digital life.

## Make successful work reusable

Hermes [skills](https://hermes-agent.nousresearch.com/docs/user-guide/features/skills) are Markdown procedures that can include triggers, steps, checks, pitfalls, scripts, and templates.

When a task succeeds, I can capture the method. When it fails because a prerequisite was missing or a command became stale, I can update the skill.

This gives “the agent learns with you” a concrete meaning: the improvement is an artifact I can read and edit, rather than a vague promise that the model will behave better next time.

## Schedule work—but verify the result

Hermes cron jobs run prompts on a schedule in fresh sessions. A scheduled task does not inherit whatever I happened to discuss on WhatsApp five minutes earlier.

That separation is useful, but completion is not the same as success. If a job should create two reports, I verify that both files exist and contain what the task requested. The scheduler status and the artifact status are different facts.

This principle applies beyond Hermes: verify the result the agent was supposed to produce, not merely whether the agent stopped running.

## A practical checklist

If you are assembling your own persistent agent, decide these things explicitly:

1. Where will it run, and how will you patch and back up that machine?
2. Which messaging channel will act as the front door?
3. Which actions may it perform without confirmation?
4. Which knowledge must remain human-readable and auditable?
5. Which preferences may live in agent-native memory?
6. How will successful procedures become reusable skills?
7. How will scheduled work prove that it produced the requested artifact?

The model matters, but these surrounding decisions determine whether the system remains useful after the first impressive conversation.

My complete setup, including both diagrams and component links, is here:

https://alfonsograna.com/my-hermes-agent-setup/

---

## Companion post

If you are looking at OpenClaw because you want a self-hosted personal AI agent, Hermes deserves a look too.

I run Hermes on a Fedora mini PC and talk to it through WhatsApp. The useful part is the system around the model: inspectable memory, reusable skills, tools, scheduled work, and verification.

Here is the architecture—and a checklist for building your own:

https://alfonsograna.com/my-hermes-agent-setup/

## Suggested media

1. Hero: architecture diagram.
2. Mid-article: agent-loop diagram.
3. Optional: redacted screenshot of the WhatsApp interface.
