---
layout: default
title: Sync Tool
nav_order: 3
---

# Sync Tool
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Installation

Download the `agblogger` program for your operating system from the [releases page](https://github.com/agblogger/agblogger/releases). It's a standalone executable — no dependencies needed.

---

## Setup

Initialize a local directory and link it to your server:

```bash
agblogger init --dir ~/blog --server https://your-server.com
```

You'll be prompted to log in.

---

## Workflow

Write or edit `.md` files in the `posts/` folder, then:

```bash
agblogger status --dir ~/blog          # preview what will change
agblogger sync --dir ~/blog            # push local changes, pull remote changes
agblogger sync --dir ~/blog --yes      # skip confirmation prompt
```

---

## Post format

Each post lives in its own directory inside `posts/`. The directory contains an `index.md` file and any images or other files that belong to the post:

```text
posts/
└── 2026-03-01-example-post/
    ├── index.md
    └── diagram.png
```

Images and other files placed next to `index.md` are uploaded with the post on sync, so you can reference them directly in your Markdown (e.g. `![](diagram.png)`).

The `index.md` file is a Markdown file with optional front matter at the top:

```markdown
---
title: My Post Title
labels:
  - "#cooking"
  - "#recipes"
draft: true
---

Post content here.
```

Only the body is required. If `title` is omitted, it's extracted from the first `# heading` (or derived from the directory name). Dates are filled in automatically on sync.

You can also set `author`, `created_at`, and `modified_at` in the front matter, but these are typically managed by the server.

---

## Conflict handling

When the same post is edited both locally and on the web between syncs, the sync tool performs a three-way merge — comparing your local version, the server version, and the last-synced common ancestor. Most of the time this merge succeeds automatically and no action is needed.

A conflict only occurs when the automatic merge fails (for example, when both sides changed the same paragraph). In that case the sync tool overrides the local file with server version, prints a warning, and saves the local file to the `.backups` folder.

---

## Authentication

When you run a command, you'll be prompted for your username and password. You can also authenticate with a Personal Access Token via the `--pat` flag or `AGBLOGGER_PAT` environment variable.
