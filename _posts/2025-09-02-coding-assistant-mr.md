---
layout: post
title: "the agent and cli"
date: 2025-09-02 10:51:00 -0000
categories: ai coding-assistants coding
tags: [gh, github, cli, mcp, tokens, automation, productivity, coding-assistant, cursor, ai]
excerpt: "If your assistant can already execute shell commands, a CLI like gh does the same work as an MCP server for a fraction of the tokens. Here's the math."
author: "Alfonso Grana"
---

If your coding assistant can execute commands, it already has everything it needs to talk to GitHub, GitLab, your cloud, your database. You usually don't need to bolt an MCP server on top.

I run **Cursor's agent** (and the same applies to the Cursor CLI, `cursor-agent`). When I ask it to do something on GitHub, I let it shell out to **`gh`, the GitHub CLI**, instead of wiring up the GitHub MCP server. The result is the same actions with far less context overhead.

## What an agent can actually do with `gh`

These are not hypothetical. Every one of these is something I let the agent run on its own:

```bash
# Open a pull request, self-assigned, with a real description
gh pr create --title "[PB1225] Remove stale draft" --assignee @me --base main --body "..."

# Squash-merge it and clean up the branch
gh pr merge 4 --squash --delete-branch

# Watch CI and read only the failing logs
gh pr checks
gh run watch
gh run view --log-failed

# Triage review feedback
gh pr view --comments
gh api repos/agrana/alfonsograna/pulls/4/comments

# File and inspect issues
gh issue create --title "Index skips extensionless posts" --body "..."
gh issue list --state open
```

The agent strings these together: open the PR, watch the checks, pull the failing log, fix, push, merge. No extra integration, no schemas to load. It just runs the command and reads the output.

## The token math: CLI vs the GitHub MCP server

Here's the part people underestimate. An MCP server's tool definitions get loaded into the model's context **on every turn**, whether or not you touch GitHub in that turn. You pay the schema tax constantly.

GitHub publishes the numbers for its own MCP server ([discussion #1182](https://github.com/github/github-mcp-server/discussions/1182)):

| | GitHub MCP server | `gh` CLI |
|---|---|---|
| Tools loaded by default | 52 (was 101) | 0 |
| Context cost just to exist | ~30.3k tokens (was ~64.6k) | ~0 tokens |
| When you pay | Every turn | Only when a command runs |
| What you pay | Tool schemas + structured response | The command string + its output |

The model already knows `gh` from training, so the capability costs nothing to "install" into context. You only spend tokens when the agent actually runs a command, and only on the command plus the output you asked for. With the MCP server you start every request already ~30k tokens in the hole — that's less room for your actual code and conversation, slower responses, and higher cost.

GitHub itself recommends trimming the server with `--tools`/`X-MCP-Tools` to cut 60–90% of that overhead. That's good advice, but it's effort spent clawing back tokens that a CLI never spent in the first place.

## When the MCP server still wins

To be fair, MCP isn't pointless:

- **No shell available.** If your assistant can't execute commands, MCP is how it reaches GitHub at all. This whole post assumes it can — hence the title.
- **Structured output and guardrails.** MCP returns typed responses and lets you enforce read-only mode or scope tools. A raw CLI gives the agent a shell, which is more power and more risk.
- **Auth handling.** The server manages tokens and scopes for you.

So the rule is conditional: **if your agent can already run commands, prefer the CLI.** Reach for MCP when it can't, or when you specifically want the structure and guardrails.

## Telling Cursor to use this

A short Cursor rule makes it the default behavior:

```md
---
alwaysApply: true
---

# GitHub via CLI

- Prefer the `gh` CLI for all GitHub tasks (PRs, issues, checks, releases).
- Do not use the GitHub MCP server unless `gh` cannot do the job.
- Open PRs with `gh pr create`, self-assign, write a real description.
- Squash-merge with `gh pr merge --squash`.
```

On GitLab the same idea applies — let the agent open the MR straight from `git push` with push options instead of a server:

```bash
git push \
  -o merge_request.create \
  -o merge_request.target=main \
  -o merge_request.squash \
  -o merge_request.title="[JIRA-KEY] title"
```

Same outcome, near-zero context tax. If the command already does the job, let the agent run the command.
