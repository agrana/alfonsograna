---
layout: post
title: "If your coding assistant execute commands do this"
date: 2025-09-02 16:51:00 -0002
categories: ai coding-assistants coding
---

# Let your coding assistant to run local commands is always cheaper than MCP

When you have a cli command to do what you need to as the external tools is always cheaper in tokens to instruct the assistant to use the command insteand of using  an MCP server.

# Let your coding assistants open your MRs in Gitlab

When working with cursor I usually create my branches and commit messages with the AI
So convinient and my commit messages are so much better one line per change explaining clearly what I want to achieve is awesome
But i kept jumping to gitlab web to open the MR after finishing my branch.
Not anymore
Today I learn that you can open an MR when you push [gitlab push options](https://docs.gitlab.com/topics/git/commit/#push-options)

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

If this does not work you can also use the cli `glab mr` command.

# Telling Cursor to use this

So now I can crete a rule in cursor to instruct the model to crete MRs when I mention it.

```md
---
alwaysApply: true
---
- Always create changes in a feature branch that mentions the Jira Issue the user is working on.
- Include sound clear description in your commit messages alway commit and push atoomically.
- When finishing the feature push your changes creating a merge request like:
git push \
  -o merge_request.create \
  -o merge_request.target=main \
  -o merge_request.auto_merge \
  -o merge_request.squash
  -o merge_request.assing="my_user"
  -o merge_reuqest.tittle="title"
  -o merge_request.description="description"
- Keep using the same merge request if more changes are needed on the same task.
```

