---
layout: post
title: "How I Run Honcho for Hermes: Persistent Memory and Personalities"
date: 2026-08-02 15:55:56 +0200
categories: [ai, self-hosting, devops]
tags: [honcho, hermes-agent, memory, netdata, podman]
excerpt: "I self-host Honcho to give Hermes persistent memory and directional models of both participants. This is how the memory system, services, session boundaries, backups, and monitoring fit together on my machine."
author: "Alfonso Grana"
---

# How I Run Honcho for Hermes: Persistent Memory and Personalities

In [my Hermes setup]({{ '/my-hermes-agent-setup/' | relative_url }}), I described Honcho as the part that gives Hermes persistent memory. At the time, I was still learning what happened behind that interface.

I have since worked through the system from message ingestion to recall, including the background processing, session boundaries, backups, and monitoring. This article describes the setup I built and how I use it.

Hermes is the agent runtime. It handles conversations, tools, and work. [Honcho](https://honcho.dev/) is the memory layer. It stores what happened, derives useful conclusions, models the participants, and returns relevant context to Hermes later.

## What Honcho stores

Honcho receives messages from Hermes inside a workspace. It turns those messages into several forms of memory:

- Messages preserve the conversation history.
- Embeddings support semantic retrieval.
- Conclusions capture facts and patterns derived from conversations.
- Session summaries preserve the shape of longer work.
- Peer representations model what one participant knows or believes about another.
- Dialectic recall builds context for the current conversation from stored memory.

Hermes remains responsible for acting. Honcho gives its work continuity.

## My self-hosted architecture

I run the Honcho data plane on the same Fedora machine as Hermes.

```text
Hermes
  |  messages and recall requests
  v
Honcho API  <--------------------+
  |                              |
  +--> PostgreSQL + pgvector     |
  +--> Redis                     |
                                 |
Honcho deriver and dreaming -----+
  |
  +--> OpenAI language and embedding models
```

The parts have separate responsibilities:

- The Honcho API receives messages and serves recall requests.
- PostgreSQL is the durable store. Its pgvector extension stores embeddings.
- Redis carries background work.
- The deriver turns new material into conclusions and representations.
- Dreaming revisits accumulated conclusions and consolidates them.

The API and deriver run as systemd user services. PostgreSQL 15 with pgvector and Redis 8.2 run in named rootless Podman containers.

The API listens on `127.0.0.1:8000`, which keeps it local to this computer. It runs from the project's Python environment with one production FastAPI worker. One worker fits the current load because Hermes is the only writer. It also keeps memory use, database pools, and process-local metrics predictable.

Switching from the development reloader to the production server reduced the API's memory use from roughly 600 MB to about 250–310 MB. Its task count dropped from 18 to 9.

The systemd unit gives the API a read-only view of the system and home directories, private temporary storage and devices, and prevention of new privileges. The service receives explicit access to the paths it needs to write.

The data stays in my local PostgreSQL instance. Language processing and embeddings use OpenAI models, so the complete system is connected: storage and orchestration are local, while model computation is external.

At the time of writing, I run Honcho server 3.0.9, its Python SDK 2.1.2, and Hermes 0.16.0.

## Peers make memory directional

My Honcho workspace is named `hermes`. It contains two peers:

- `Alfons` represents me.
- `hermes-whatsapp` represents Hermes.

Honcho maintains four directional relationships between them:

1. My model of myself.
2. Hermes's model of me.
3. My model of Hermes.
4. Hermes's model of itself.

Direction gives a statement its owner and subject. My preferences become part of Hermes's model of me. A separate relationship contains Hermes's account of its own role and working behavior.

Each relationship can have a peer card and a larger body of conclusions. A peer card contains a small set of stable identity facts. Conclusions contain observations and deductions that can evolve. My cards can remain sparse while thousands of conclusions accumulate. I prefer an empty card to one filled with temporary facts.

Only Hermes currently writes to this workspace. In the future, coding agents, notes, email imports, or other assistants could feed the same Alfons peer. Each source would use its own sessions and peer identity where appropriate. That would create one personal memory layer while preserving the source of each interaction.

## Recall and background processing

Hermes uses Honcho's hybrid recall mode. It combines immediate session context with semantically relevant long-term memory.

My configuration provides up to 1,200 tokens of Honcho context. Dialectic recall runs at the beginning of a session and then every two turns, including nested work. Its depth is one, which keeps the process bounded. A reasoning heuristic starts with a low effort and permits a higher level when the request needs it.

Hermes writes messages asynchronously, so it can continue working while Honcho processes new material. The deriver groups representation work around a 512-token target. A small pending batch is a normal state because the deriver waits for enough material to form a useful batch.

Dreaming handles slower consolidation. A cycle becomes eligible after 50 new explicit conclusions, with an eight-hour cooldown and a 60-minute idle period. It can reconcile existing conclusions, derive new ones, and update stable representations. I verified that these cycles run on my installation.

## Sessions for work across repositories

I often start in one repository and discover that the task requires a change in another. The session strategy preserves the connection between those changes and gives every project its own default memory stream.

I configured Hermes to use a per-repository session strategy. I also mapped `/home/alfonso` to a `personal` session.

When I start Hermes inside a repository, that repository normally determines the Honcho session. Starting it in the Honcho repository resolves to the `honcho` session. The session key is selected at startup. Moving to another directory inside the same Hermes process keeps that session.

My working rules are:

1. Start Hermes inside the repository that owns the task.
2. Keep the session when another repository contributes to the same outcome.
3. Give a significant cross-repository effort a clear title so it becomes one named workstream.
4. Start a separate Hermes process for unrelated work in another repository.
5. Map a shared parent directory when a group of repositories forms one permanent work area.

This makes the workstream the task boundary and the repository the default starting point.

Manual directory mappings have the highest precedence in the session resolver. A title comes next, followed by gateway and session identifiers, then the configured repository or directory strategy. I start repository work inside the repository because my home directory has an intentional mapping to `personal`.

## Backups and memory maintenance

A daily systemd timer runs a custom PostgreSQL backup with `pg_dump`. It stores private backup files under `~/.local/share/honcho/backups`, retains 14 days, and catches up after downtime because the timer is persistent.

I tested the recovery path by restoring the first backup into a temporary database and comparing its record counts with the running database.

The backups currently share a disk with the database. They protect against database mistakes and corruption. An encrypted off-machine copy would extend that protection to loss of the computer or disk.

A monthly timer creates a read-only memory report under `~/.local/share/honcho/reviews`. It covers all four directional peer cards and the latest conclusions for each relationship. I review Hermes's model of me first because it has the greatest effect on personalized recall.

I use the following maintenance rules:

- Stable identity belongs in a peer card.
- Changing observations belong in conclusions.
- A false or outdated conclusion receives an explicit correction in its workstream.
- Dreaming gets time to reconcile the correction.
- Deletion is reserved for privacy, security, or persistently harmful false memory, with a backup taken first.
- Honcho interfaces manage Honcho records.

I also run a review after a clearly wrong recall, a major change in my work or life, or a change to the model or memory configuration. A quarterly review covers the broader direction of the memory.

## Monitoring with Netdata

[Netdata](https://www.netdata.cloud/) already monitors this machine. I added a local exporter that sends Honcho operational measurements to Netdata over StatsD every 15 seconds.

The dashboard covers:

- API, deriver, PostgreSQL, and Redis state.
- API health and response time.
- Queue depth, active work, queue age, and processing errors.
- Database size and connection counts.
- Backup age and availability.
- API request, recall, and database-pool metrics.
- Resource use for the Podman containers and Redis.

The exporter sends numbers. Messages, conclusions, session names, keys, and model responses stay out of the monitoring stream. Database checks run once per minute to keep the overhead small.

API Prometheus metrics remain enabled. Deriver metrics are disabled because their fixed port conflicts with an existing Fedora service on port 9090. Netdata still observes the deriver through the exporter and its system collectors.

The monitoring path is local. I currently inspect the dashboard and service state directly because Netdata has no external notification destination configured.

## How I use the setup

I treat Honcho as durable context and Hermes conversations as the place where that context forms.

For daily work, I start Hermes in the repository that owns the task and title long or cross-repository efforts. I state durable preferences and corrections plainly. Routine conversation creates memory; the monthly report gives me a place to inspect it.

When recall feels wrong, I check the session boundary first. A process started in another directory may be using a different memory stream. I then check the recent memory report, queue health, and dreaming activity. These checks distinguish session selection from delayed processing and an inaccurate conclusion.

I keep unrelated tasks in separate workstreams, even when I work on them at the same time. I preserve one workstream when several repositories contribute to the same result.

The current setup has a defined scope. Hermes writes the memory, local services hold the data, OpenAI supplies model computation, daily backups cover local recovery, and Netdata covers local operations. Additional writers, off-machine backups, external alerts, and modern Quadlet container units are possible next steps. I can add each one as an independent change with its own reason and verification.
