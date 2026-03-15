---
layout: home
title: Home
nav_order: 1
---

# AgBlogger

**A markdown-first blogging platform with bidirectional sync.**

AgBlogger is a self-hosted blogging platform built around plain markdown files. Posts live on disk as YAML front matter + markdown — your editor, your git history, your control. A lightweight FastAPI backend and React frontend add search, labels, cross-posting, and a web UI on top without replacing the files as the source of truth.

---

## Key features

- **Markdown-first** — posts are `.md` files in a `content/posts/` directory; the database is a read-through cache, not the master copy
- **Bidirectional sync** — a SHA-256 hash-based three-way merge engine keeps a local directory and a remote server in sync via the `agblogger` CLI
- **Label DAG** — hierarchical labels form a directed acyclic graph with an interactive visualisation in the UI
- **Cross-posting** — publish simultaneously to Bluesky, Mastodon, X, and Facebook
- **Full-text search** — SQLite FTS5 index over post content and metadata
- **Hardened auth** — HttpOnly cookie sessions for the web UI; Personal Access Tokens (PAT) for the CLI and API

## Tech stack

| Layer | Technology |
|---|---|
| Backend | Python 3.14+, FastAPI, SQLAlchemy 2.0, SQLite (WAL mode), Pandoc |
| Frontend | React 19, TypeScript, Vite, TailwindCSS 4, Zustand, React Flow |
| Infrastructure | Docker, Caddy (automatic HTTPS), `uv` |

---

## Deploy the server

Requires Docker, `just`, and the repo cloned on your host.

```bash
git clone https://github.com/agblogger/agblogger && cd agblogger
just deploy   # interactive wizard: sets admin credentials, domain, HTTPS
docker compose --env-file .env.production up -d
```

Then open `https://<your-domain>/login` and sign in with the credentials you chose.

[Full deployment guide →]({% link server-deployment.md %})

---

## Sync posts with the CLI

```bash
# One-time setup: point the CLI at your server and local content directory
agblogger --dir ~/blog --server https://your-server.com init

# Preview changes without touching anything
agblogger --dir ~/blog status

# Push local edits and pull remote changes
agblogger --dir ~/blog sync
```

The sync engine uses SHA-256 hashes for a three-way merge. On conflict the server version wins; your local copy is saved as `<file>.conflict-backup`.

[Full CLI reference →]({% link sync-cli.md %})
