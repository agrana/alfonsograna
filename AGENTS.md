# AGENTS.md

Guidance for AI coding agents (Cursor, Codex, Hermes, etc.) working in this repo.
This is the single source of truth for agent instructions—keep it concise.

## Project

Personal blog + site for Alfonso Grana, built with **Jekyll 4.3** and deployed to
**GitHub Pages**. Repo: `agrana/alfonsograna`, served under base path `/alfonsograna`
at https://agrana.github.io/alfonsograna/. Theme is the remote `pages-themes/hacker`,
with overrides in `assets/css/custom.css`.

## Layout

- `_posts/` — dated blog posts, `YYYY-MM-DD-slug.md` (appear under "Writing").
- `_layouts/` — `post.html`, `page.html`.
- `_includes/` — `featured-links.html`, `post-list.html`, `head-custom.html`, `mermaid.html`.
- `assets/` — `css/`, `images/`, `diagrams/`.
- `_config.yml` — site settings (title, baseurl, plugins, cross-posting flags).
- `index.md`, `about.md`, plus standalone pages at the root (e.g. `home-hermes-setup.md`).
- `_*-template.md` — copy-paste templates. They start with `_`, so Jekyll never
  publishes them. Use them as references; do not edit or commit them unless asked.

## Local development

```bash
bundle install              # first time
bundle exec jekyll serve    # serves at http://localhost:4000/alfonsograna/
```

Deployment is automatic: pushing to `main` triggers `.github/workflows/pages.yml`.

## Writing posts

1. Create `_posts/YYYY-MM-DD-slug.md` (kebab-case slug, real publish date).
2. Use this front matter (see `_post-template.md` for the canonical version):

```yaml
---
layout: post
title: "Post Title"
date: YYYY-MM-DD HH:MM:SS -0000
categories: [ai, devops]
tags: [tag1, tag2]
excerpt: "2-3 sentence summary shown in previews, RSS, and Medium."
author: "Alfonso Grana"
---
```

3. Body is Markdown. Conventions:
   - Open with an H1 (`#`) matching the title, then `##` sections.
   - Tag fenced code blocks with a language (`bash`, `json`, `yaml`, ...).
   - When pasting from elsewhere, strip artifacts (stray line numbers, "Copy"
     button text, etc.).
   - Standalone (non-dated) pages use `_page-template.md` and link from
     `_includes/featured-links.html`.

Cross-posting to Medium uses canonical URLs (`canonical_url: true` in `_config.yml`);
see `_cross-posting-template.md`.

## Git workflow

- Branch off `main`: `feature/<short-slug>` (e.g. `feature/cli-vs-api-vs-mcp`).
  At work a Jira key is required (`feature/<JIRA-KEY>-<slug>`); this personal repo
  has no Jira, so a plain slug is fine.
- **This repo is on GitHub, not GitLab.** Do not use GitLab `merge_request.*` push
  options here. Open PRs with `gh pr create` when review is wanted; plain `git push`
  otherwise.
- Commit messages use a lowercase type prefix matching existing history:
  `content:`, `copy:`, `style:`, `fix:`, `docs:`.
- Only commit files relevant to the task; leave unrelated untracked files alone.
- Don't commit unless explicitly asked.

## Working style

- Prefer running a CLI command directly over an equivalent MCP call when it does the
  job (more token-efficient).
- Keep changes minimal and reviewable; trim AI-generated slop and dead code before
  pushing. Don't ship code you don't understand.
- Write clearly and unambiguously; state assumptions rather than guessing silently.
- Never commit secrets.
