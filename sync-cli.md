---
layout: default
title: Sync Tool Guide
nav_order: 3
---

# Sync Tool Guide
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
agblogger --dir ~/blog --server https://your-server.com init
```

You'll be prompted to log in. Your credentials are saved locally so you only need to authenticate once.

---

## Workflow

Write or edit `.md` files in the `posts/` folder, then:

```bash
agblogger --dir ~/blog status          # preview what will change
agblogger --dir ~/blog sync            # push local changes, pull remote changes
agblogger --dir ~/blog sync --yes      # skip confirmation prompt
```

---

## Post format

Posts are Markdown files with optional front matter at the top:

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

Only the body is required. If `title` is omitted, it's extracted from the first `# heading` (or derived from the filename). Dates are filled in automatically on sync.

You can also set `author`, `created_at`, and `modified_at` in the front matter, but these are typically managed by the server.

---

## Conflict handling

If the same post is edited both locally and via the web between syncs, the sync tool flags the conflict in its output. A `.conflict-backup` copy is saved so you can compare versions and resolve manually before syncing again.

---

## Authentication

The first time you run a command, you'll be prompted for your username and password. Your login is saved locally so you won't need to sign in again.

You can also authenticate with a Personal Access Token via the `--pat` flag or `AGBLOGGER_PAT` environment variable.
