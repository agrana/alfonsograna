---
layout: post
title: "Inbox Zero With Seatbelts"
date: 2026-06-25 00:00:00 -0000
categories: [ai, agents]
tags: [ai-agents, mcp, gmail, automation, devex]
excerpt: "A local Gmail MCP reached its first real end-to-end run: OAuth, safe label creation, dry-run classification, and no email mutations without confirmation."
author: "Alfonso Grana"
---

Yesterday I wrote about why I wanted a local Gmail MCP instead of a generic Gmail integration.

Today it actually worked.

Not in the dramatic demo sense. No agent wrote replies. No inbox was magically emptied. No autonomous assistant declared victory while quietly doing suspicious things in the background.

The useful version was much more boring:

1. OAuth completed.
2. The tool created the `AI/*` Gmail labels.
3. It scanned a small batch of inbox threads in dry-run mode.
4. It proposed classifications.
5. It changed nothing until explicitly confirmed.

That is exactly the shape I want for personal automation.

## The safety contract matters

Email is not a toy domain.

A bad automation can delete context, send the wrong thing, archive something important, or simply make a mess that is harder to reason about than the original inbox.

So the first rule is restraint.

This local Gmail MCP has a deliberately boring safety contract:

- dry-run by default
- label-only unless explicitly applied
- never sends email
- never deletes email
- no auto-archive unless explicitly enabled
- uncertain threads go to `AI/Review`
- decisions can be explained later

That makes it feel less like “an AI has access to my inbox” and more like “I have a small local operator that prepares safe proposals”.

That distinction is everything.

## The first real run

After the OAuth flow completed, the setup created the Gmail labels that act as the workflow state machine:

- `AI/Managed`
- `AI/Needs Reply`
- `AI/Action`
- `AI/Waiting`
- `AI/Read Later`
- `AI/FYI`
- `AI/Newsletter`
- `AI/Receipt`
- `AI/Calendar`
- `AI/Review`
- `AI/Important`
- `AI/Failed Processing`
- `AI/Archive Candidate`
- `AI/Do Not Auto Archive`

Then I ran the first preview scan.

Ten unmanaged inbox threads were classified as newsletters with high confidence. The tool recommended archive eligibility, but because the run was dry-run, no labels were applied and nothing was archived.

That is the right default.

Show the work first. Ask before touching anything.

## Inbox zero is not an empty inbox

The more I build this, the more I like a different definition of inbox zero:

Inbox zero is not “no email”.

Inbox zero is “nothing unmanaged”.

Every thread should either still be unknown or have a clear state. Needs reply. Waiting. Read later. Receipt. Newsletter. Review. Managed.

Labels become the state machine. The inbox stops being a pile and starts becoming a queue with explicit semantics.

That is a much better target for agents than “clean up my inbox”.

“Clean up my inbox” is vague and dangerous.

“Classify unmanaged threads, explain each decision, and wait for confirmation before applying labels” is a workflow.

## Tiny sharp tools beat magic boxes

The interesting part here is not that an LLM can read email snippets.

The interesting part is the boundary design.

A useful personal agent should not need unlimited authority. It needs small, sharp, governed capabilities:

- scan
- classify
- label
- explain
- review
- report stats

Each operation should be scoped. Each mutation should be intentional. Each decision should leave a trail.

That is slower than a flashy demo, but it is much closer to something I would actually trust.

## What comes next

The next step is to process a small confirmed batch and inspect the review queue.

After that, the work becomes tuning:

- better rules for newsletters and receipts
- better detection of emails that need a reply
- stricter handling of anything important
- scheduled dry-run sweeps
- eventually, maybe carefully gated auto-archive for low-risk categories

But the foundation is now there.

A local Gmail MCP can authenticate, create the workflow labels, scan real inbox threads, and propose safe decisions without mutating anything by default.

That is the kind of automation I want more of.

Not magic.

Not a black box.

Inbox zero with seatbelts.
