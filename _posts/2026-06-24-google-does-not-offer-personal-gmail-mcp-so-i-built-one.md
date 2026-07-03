---
layout: post
title: "Personal Gmail MCP Is Not Possible Yet, So I Built My Own"
date: 2026-06-24 00:00:00 -0000
categories: [ai, agents]
tags: [ai-agents, mcp, gmail, cli, devex]
excerpt: "A condensed journey from trying to use Gmail through MCP to realizing the better architecture was to build a local Inbox Zero MCP server with Gmail API, labels, audit, and safety-first automation."
author: "Alfonso Grana"
---

I wanted a simple thing: let an AI agent help me reach inbox zero in Gmail.

Not by writing replies for me. Not by deleting mail. Not by pretending email is magically solved by a chatbot.

The goal was smaller and more useful: make sure nothing in the inbox is unmanaged.

Every incoming thread should end up in a known state:

- needs reply
- action required
- waiting
- read later
- FYI
- newsletter
- receipt
- calendar
- review
- managed and archived

That sounds like the kind of workflow an agent should be good at. It can inspect messages, apply labels, explain why something was classified, and keep a review queue for uncertain cases.

So I looked for the obvious integration: Gmail MCP.

## The first surprise

Google has published Gmail MCP material and there is a Gmail MCP endpoint. But for a personal Gmail account, MCP access is not possible right now in the way I needed it: a simple personal account integration that an agent can connect to and use reliably.

I could get part of the MCP layer to respond. The server connected. Tool discovery worked. The agent could see Gmail-shaped tools like listing labels, searching threads, and creating drafts.

But discovery is not access.

A server can advertise tools while the real Gmail call still fails because the account, OAuth flow, permissions, or product availability do not support the use case.

In my case, the transport worked and the tool list appeared, but a harmless real call such as listing labels did not complete successfully. That means the integration was visible to the agent, but not usable for my personal Gmail workflow.

That is the useful lesson: for agents, seeing a tool exists is not the same thing as being able to use it.

## The second surprise

The more I looked at the problem, the more obvious it became that I did not actually want a generic Gmail MCP.

I wanted an Inbox Zero capability.

Those are different things.

A generic Gmail MCP exposes Gmail operations. Useful, but low-level.

An Inbox Zero MCP should expose intent-oriented tools:

- scan unmanaged inbox threads
- process one thread
- process a batch
- show the review queue
- explain a decision
- add a rule
- show stats

The agent should not have to assemble raw Gmail operations every time. It should call a capability that already understands the workflow, the labels, the safety model, and the audit trail.

That is where MCP becomes more interesting.

MCP is not only a way to wrap APIs. It is a way to present capabilities to agents.

## CLI, API, and MCP each have their place

This journey also clarified a broader pattern I keep seeing.

Agents do not need one integration surface. They need several.

The CLI gives agents hands. It lets them operate existing tools, inspect local state, and run workflows the same way a human operator would.

The API gives agents contracts. It is structured, scoped, versioned, and easier to secure and audit.

MCP gives agents a map. It tells them what capabilities exist, what they are for, and how to call them.

For Gmail inbox zero, the right architecture is not “MCP instead of API”.

The better architecture is:

- Gmail API for the actual email operations
- SQLite for local audit, idempotency, and decisions
- CLI for local testing and scheduled runs
- MCP as the agent-facing capability layer

The MCP server becomes the clean surface the agent sees. Internally, it can use boring, proven building blocks.

That is usually the right answer.

## The design I wanted

The server should be local and conservative by default.

It should call Gmail API directly, not depend on a remote Gmail MCP service.

It should use labels as a state machine:

- AI/Managed
- AI/Needs Reply
- AI/Action
- AI/Waiting
- AI/Read Later
- AI/FYI
- AI/Newsletter
- AI/Receipt
- AI/Calendar
- AI/Review
- AI/Important
- AI/Failed Processing

The invariant is simple:

Every processed thread gets `AI/Managed` and one primary state label.

That gives the inbox a useful property: either something is unmanaged, or it has an explicit state.

Inbox zero stops meaning “empty at all costs”. It means “nothing is unknown”.

## Safety first

Email automation can get dangerous quickly.

So the first version should be boring:

- dry-run by default
- label-only by default
- never delete
- never send replies
- never auto-archive unless explicitly enabled
- keep an audit log of every decision
- put uncertain threads into review

This is not because the agent is weak. It is because the workflow touches personal and professional communication.

A good agentic system should start with reversible actions and earn trust.

Labels are a good first control surface. They let the system create structure without pretending it has full authority.

## Event-based and scheduled

There are two ways this should eventually run.

Event-based processing is the fast path. Gmail receives a new message, a notification fires, and the server processes the thread.

Scheduled processing is the correctness path. Every few minutes, or every hour, the system scans for anything still unmanaged.

You want both.

Events are responsive, but they can be missed. Schedules are less elegant, but they close gaps.

The goal is not architectural purity. The goal is no unmanaged email.

## The agent-facing tools

The MCP surface should expose workflow-level tools, not just Gmail primitives.

For example:

- `inboxzero_scan`
- `inboxzero_process_thread`
- `inboxzero_process_batch`
- `inboxzero_get_review_queue`
- `inboxzero_apply_decision`
- `inboxzero_list_rules`
- `inboxzero_add_rule`
- `inboxzero_stats`
- `inboxzero_explain_thread`

Those tools make sense to an agent because they describe the work, not merely the transport.

That is the core design shift.

Do not expose “raw system access” and hope the agent invents your workflow every time. Expose a governed capability.

## The real lesson

The interesting part of this project is not Gmail.

The interesting part is the pattern.

A lot of agent integrations will start this way:

1. try the generic tool
2. discover it is either too low-level, too immature, or not available for the exact use case
3. realize the workflow needs its own capability layer
4. build a small local MCP server that wraps APIs, CLIs, rules, and policy
5. expose that as a safe tool for agents

That is where MCP becomes practical.

Not as magic. Not as a replacement for APIs. Not as a universal plugin story.

As a way to give agents a map of useful, governed capabilities.

## Conclusion

MCP access for my personal Gmail account was not possible in the simple way I needed.

So I built the shape I actually needed: an Inbox Zero MCP server that treats email as a managed workflow, not just a mailbox.

The design is simple:

- Gmail API does the real work
- labels represent state
- SQLite records decisions
- CLI supports local and scheduled runs
- MCP gives agents the capability surface

That is the pattern I expect to repeat often.

When the generic integration is missing or too raw, build the capability yourself.

Give the agent a map. Give it safe tools. Make every action explainable.

Inbox zero is not an empty inbox.

Inbox zero is nothing unmanaged.
