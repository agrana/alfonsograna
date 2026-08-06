# AGENTS.md

Guidance for AI coding agents (Cursor, Codex, Hermes, etc.) working in this repo.
This is the single source of truth for agent instructions—keep it concise.

## Project

Personal blog + site for Alfonso Grana, built with **Jekyll 4.3** and deployed to
**GitHub Pages**. Repo: `agrana/alfonsograna`, served from the domain root at
https://alfonsograna.com/. CI uses Ruby 3.2. The site uses the remote
`pages-themes/hacker` theme with an editorial STIX Two Text design system and
overrides in `assets/css/custom.css`.

## Layout

- `_posts/` — dated blog posts, `YYYY-MM-DD-slug.md` (appear under "Writing").
- `_drafts/` — unpublished working drafts; include them locally with `--drafts`.
- `_layouts/` — `post.html`, `page.html`.
- `_includes/` — `featured-links.html`, `post-list.html`, `head-custom.html`, `mermaid.html`.
- `assets/` — `css/`, `images/`, `diagrams/`.
- `_config.yml` — site settings (title, baseurl, plugins, cross-posting flags).
- `index.md`, `about.md`, plus standalone pages at the root (e.g. `my-hermes-agent-setup.md`).
- `robots.txt` — crawler rules and sitemap location.
- `_*-template.md` — copy-paste templates. They start with `_`, so Jekyll never
  publishes them. Use them as references; do not edit or commit them unless asked.

## Local development

```bash
bundle install              # first time
bundle exec jekyll serve    # serves at http://localhost:4000/
bundle exec jekyll build    # production-equivalent build check
bundle exec jekyll serve --drafts  # preview files in _drafts/
```

`.github/workflows/pages.yml` builds the site for pull requests, pushes to `main`,
and manual runs. Deployment happens only for pushes to `main`.

## Writing posts

1. Create `_posts/YYYY-MM-DD-slug.md` (kebab-case slug, real publish date).
2. Use this front matter (`image` is optional; see `_post-template.md` for a
   starting point):

```yaml
---
layout: post
title: "Post Title"
date: YYYY-MM-DD HH:MM:SS -0000
categories: [ai, devops]
tags: [tag1, tag2]
excerpt: "2-3 sentence summary shown in previews, RSS, and Medium."
author: "Alfonso Grana"
# image: /assets/images/post-hero.webp
---
```

3. Body is Markdown. Conventions:
   - `_layouts/post.html` renders the front-matter title as the page H1. Existing
     posts are mixed on repeating that H1 in the body, so check the rendered page
     and avoid introducing an accidental duplicate.
   - Use `##` for top-level body sections.
   - Tag fenced code blocks with a language (`bash`, `json`, `yaml`, ...).
   - Mermaid diagrams are supported in fenced `mermaid` blocks.
   - Use `relative_url` for internal page and asset links so local and production
     rendering stay consistent.
   - When pasting from elsewhere, strip artifacts (stray line numbers, "Copy"
     button text, etc.).
   - Standalone (non-dated) pages use `_page-template.md` and link from
     `_includes/featured-links.html`.

## Writing style

- Use affirmative definitions: state what a subject is, what it does, and what it
  enables.
- Revise habitual contrastive negation, including “X, not Y,” “not just X but Y,”
  “No X, more Y,” negation-led hooks, and lists of what something is not. Rewrite
  around the subject's positive identity, action, capability, or outcome.
- Use negation when it communicates a factual prohibition, unsupported capability,
  technical or contractual limit, direct correction, or meaningful decision
  boundary. State the limit once and near the action it governs.
- Prefer concrete capabilities and outcomes in calm, precise language. Use a short
  contrast only when the contrast changes or clarifies the meaning.
- Name concrete tasks, deliverables, components, interfaces, responsibilities, and
  failure boundaries instead of relying on vague references such as “the work” or
  generic architectural “layers.”

Treat `https://alfonsograna.com/` as the canonical site URL. Medium cross-posting
is a manual import workflow; see `_cross-posting-template.md` and the flags in
`_config.yml`. Do not introduce the old `agrana.github.io/alfonsograna/` URL.

## Git workflow

- Branch off `main` with a prefix matching the work: `feature/`, `fix/`, `style/`,
  `docs/`, or `revise/`. Use a short kebab-case slug; this personal repo does not
  require a Jira key.
- **This repo is on GitHub, not GitLab.** Do not use GitLab `merge_request.*` push
  options here. Open PRs with `gh pr create` when review is wanted; plain `git push`
  otherwise.
- Commit messages use a lowercase type prefix matching existing history:
  `content:`, `copy:`, `style:`, `fix:`, `docs:`, `feat:`.
- Only commit files relevant to the task; leave unrelated untracked files alone.
- Don't commit unless explicitly asked.

## Working style

- Prefer running a CLI command directly over an equivalent MCP call when it does the
  job (more token-efficient).
- Keep changes minimal and reviewable; trim AI-generated slop and dead code before
  pushing. Don't ship code you don't understand.
- Write clearly and unambiguously; state assumptions rather than guessing silently.
- Never commit secrets.
