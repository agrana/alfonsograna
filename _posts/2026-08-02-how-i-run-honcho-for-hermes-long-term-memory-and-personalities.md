---
layout: post
title: "How I Run Honcho for Hermes: Long-Term Memory and Personalities"
date: 2026-08-02 15:55:56 +0200
categories: [ai, self-hosting, devops]
tags: [honcho, hermes-agent, memory, netdata, podman]
excerpt: "I self-host Honcho as Hermes's external memory provider. This is how it stores conversations, builds directional models of the user and agent, and inserts recalled context into each model request."
author: "Alfonso Grana"
---

# How I Run Honcho for Hermes: Long-Term Memory and Personalities

In [my Hermes setup]({{ '/my-hermes-agent-setup/' | relative_url }}), I described Honcho as the part that gives Hermes persistent memory. At the time, I was still learning what happened behind that interface.

I have since worked through the system from message ingestion to recall, including the background processing <Is this dreaming?>, session boundaries, backups, and monitoring. This article describes the setup I built and how I use it.

[Honcho](https://honcho.dev/) is its external memory provider. It stores what happened, derives useful conclusions, models the participants, and returns relevant context to Hermes later.

## What Honcho stores

Honcho receives messages from Hermes inside a workspace. It turns those messages into several forms of memory:

- Messages preserve the conversation history.
- Embeddings support semantic retrieval.
- Conclusions capture facts and patterns derived from conversations.
- Session summaries preserve the shape of longer work.
- Peer representations model what one participant knows or believes about another.
- Dialectic recall builds context for the current conversation from stored memory.

Hermes acts on the recalled context. Honcho provides continuity across conversations.

## My self-hosted architecture

I run the Honcho data plane on the same Fedora machine as Hermes.

```mermaid
flowchart TD
    hermes["Hermes memory-provider adapter"]
    api["Honcho API"]
    postgres[("PostgreSQL + pgvector")]
    redis[("Redis")]
    workers["Honcho deriver and dreaming"]
    openai["OpenAI language and embedding models"]

    hermes -->|"Messages, context requests, and tool calls"| api
    api --> postgres
    api --> redis
    workers --> api
    workers --> openai
```

The parts have separate responsibilities:

- The Honcho API receives messages and serves recall requests.
- PostgreSQL is the durable store. Its pgvector extension stores embeddings.
- Redis provides a shared cache for the API and deriver.
- The deriver turns new material into conclusions and representations.
- Dreaming revisits accumulated conclusions and consolidates them.

## Peers make memory directional

Honcho organizes memory in workspaces that isolate their records. This workspace
has two peers:

- `Myself` represents me.
- `Hermes` represents Hermes.

Each model owner observes a peer and maintains a directional representation:

```mermaid
flowchart LR
    subgraph owners["Model owner"]
        direction TB
        meOwner["Myself"]
        hermesOwner["Hermes"]
    end

    subgraph models["Directional representation"]
        direction TB
        meOfMe["My model of myself"]
        meOfHermes["My model of Hermes"]
        hermesOfMe["Hermes's model of me"]
        hermesOfHermes["Hermes's model of itself"]
    end

    subgraph subjects["Modeled peer"]
        direction TB
        meSubject["Myself"]
        hermesSubject["Hermes"]
    end

    meOwner --> meOfMe --> meSubject
    meOwner --> meOfHermes --> hermesSubject
    hermesOwner --> hermesOfMe --> meSubject
    hermesOwner --> hermesOfHermes --> hermesSubject
```

Each relationship can have a peer card and a larger body of conclusions. A peer card contains a small set of stable identity facts. Conclusions contain observations and deductions that can evolve.

## What Hermes inserts into the model context

The installed Hermes integration has two context paths: a static provider notice and live recalled memory.

At startup, Hermes reads `memory.provider: honcho`, loads the Honcho adapter, resolves the session and registers five Honcho tools. It adds a short notice to the cached system prompt stating that Honcho runs in hybrid mode, automatic recall is active, and memory tools are available. This notice describes the capability. It contains no recalled facts.

Hermes then starts a background prewarm for the selected Honcho session. The prewarm requests base context and a dialectic synthesis so the first useful turn can begin with memory available.

For each non-trivial user turn, the following sequence runs:

```mermaid
sequenceDiagram
    participant H as Hermes
    participant M as Memory manager
    participant A as Honcho adapter
    participant O as Honcho
    participant L as Main model

    H->>M: Pass original user message
    M->>A: Request recalled context
    A->>A: Consume prepared base context
    Note right of A: Session summary<br/>User representation<br/>User peer card<br/>AI self-representation<br/>AI identity card
    A-->>O: Start semantic refresh using the user message
    O-->>A: Return context to cache for a later turn
    opt Prepared dialectic supplement is ready
        O-->>A: Return peer.chat() result based on the user model
    end
    A->>A: Join context and enforce the 1,200-token budget
    A-->>M: Return recalled context
    M->>M: Wrap it in a memory-context block with a system note
    M-->>H: Return recalled-memory block
    H->>H: Append block to an API-only copy of the user message
    H->>L: Send stable system prompt, history, and enriched user message
```

The shape of the API-only user message is approximately:

```text
<the user's current message>

<memory-context>
[System note identifying this as recalled reference data]

## Session Summary
...

## User Representation
...

## User Peer Card
...

## AI Self-Representation
...

## AI Identity Card
...

<dialectic supplement>
</memory-context>
```

This placement preserves a byte-stable system prompt for upstream prompt caching. The injected block exists only in the model request. Hermes rebuilds the same API-only message for each model call in the tool loop, so the recalled context remains available while it uses tools. Hermes keeps the original user message unchanged in its session history, removes recalled-memory blocks from streamed output, and strips them before sending the completed conversation back to Honcho.

Trivial prompts such as acknowledgements and slash commands skip automatic injection. An unavailable Honcho service produces an empty recall result while Hermes continues the conversation.

Hybrid mode also exposes `honcho_profile`, `honcho_search`, `honcho_context`, `honcho_reasoning`, and `honcho_conclude`. When Hermes calls one of these tools, its result enters the conversation as a normal tool response. This explicit tool path complements the automatic context block.

## How memory advances after a turn

After Hermes completes a response, the memory manager sends the original user message and final assistant response to Honcho on a background worker. Interrupted turns stay out of the durable memory stream because their tool chain or response may be incomplete.

The same worker starts recall for the next turn. Base context refreshes every turn in my configuration. Dialectic synthesis starts at session initialization and then becomes eligible every two turns. The next non-trivial turn consumes the prepared result. A single dialectic pass starts at low reasoning effort, and the query-length heuristic can raise it as far as high.

The deriver processes saved messages into conclusions and representations. It groups representation work around a 512-token target, so a small pending batch is a normal waiting state.

Dreaming handles slower consolidation. A cycle becomes eligible after 50 new explicit conclusions, with an eight-hour cooldown and a 60-minute idle period. It can reconcile existing conclusions, derive new ones, and update stable representations. I verified that these cycles run on my installation.

## Sessions for work across repositories

I often start in one repository and discover that the task requires a change in another. The session strategy preserves the connection between those changes and gives every project its own default memory stream.

I configured Hermes to use a per-repository session strategy. I also mapped `/home/<username>` to a `personal` session.

When I start Hermes inside a repository, that repository normally determines the Honcho session. Starting it in the Honcho repository resolves to the `honcho` session. The session key is selected at startup. Moving to another directory inside the same Hermes process keeps that session.


This makes the workstream the task boundary and the repository the default starting point.

Manual directory mappings have the highest precedence in the session resolver. A title comes next, followed by gateway and session identifiers, then the configured repository or directory strategy. I start repository work inside the repository because my home directory has an intentional mapping to `personal`.
