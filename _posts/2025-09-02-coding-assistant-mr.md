---
layout: post
title: "If your coding assistant execute commands do this"
date: 2025-09-02 10:51:00 -0000
categories: ai coding-assistants coding
tags: [git, gitlab, cli, automation, productivity, coding-assistant, cursor, ai]
excerpt: "Learn how to make your coding assistant more efficient by using CLI commands and GitLab push options for creating merge requests automatically."
---

## Prefer local CLI over MCP

If a CLI command already does the job, it’s usually more token-efficient to have the assistant run that command directly rather than use an MCP server.

## Let your assistant open MRs from `git push` (GitLab)

With Cursor, I let the AI create branches and write commit messages—super convenient, and the one-line summaries per change are much clearer. I used to hop into GitLab’s web UI to open the MR. Not anymore: you can open it right from git push with:

```bash
git push \
  -o merge_request.create \
  -o merge_request.target=main \
  -o merge_request.auto_merge \
  -o merge_request.squash
  -o merge_request.assing="my_user"
  -o merge_reuqest.tittle="title"
  -o merge_request.description="description"
```

## Telling Cursor to use this

Now I can add a Cursor rule so the model opens a merge request whenever I ask.
…plus a few other corporate quirks..

```md
---
alwaysApply: true
---

# Branching

- Always work on a feature branch named:
  feature/<JIRA-KEY>-<short-slug>
- If you don't know the Jira Issue ask me.
- If branch is wrong (missing JIRA key), stop and create the correct branch.

# Pushes

- Use normal pushes anytime:
  git push
- Only when I'm ready to request review, open/update the Merge Request:
With:
 git push \
  -o merge_request.create \
  -o merge_request.target=main \
  -o merge_request.auto_merge \
  -o merge_request.squash
  -o merge_request.assing="my_user"
  -o merge_reuqest.tittle="title"
  -o merge_request.description="description" 

- Title should start with "[<JIRA-KEY>] ".
```
