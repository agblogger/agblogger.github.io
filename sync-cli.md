---
layout: default
title: Sync CLI
nav_order: 3
---

# Sync CLI
{: .no_toc }

The `agblogger` command-line tool keeps a local directory of markdown files in sync with a remote AgBlogger server. It uses SHA-256 hashes and a three-way merge strategy to detect and resolve conflicts.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Installation

### Standalone binary (no Python required)

Download the pre-built binary for your platform from the [releases page](https://github.com/agblogger/agblogger/releases) and place it on your `PATH`.

### From source

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
uv sync          # or: pip install -e .
```

After installation the `agblogger` command is available in your shell.

### Build from source

```bash
just build-cli
# produces dist/cli/agblogger (or dist/cli/agblogger.exe on Windows)
```

---

## Global flags

These flags are available on every command:

| Flag | Short | Default | Description |
|---|---|---|---|
| `--dir` | `-d` | current directory | Path to the local content directory |
| `--server` | `-s` | — | Server base URL, e.g. `https://your-server.com` |
| `--username` | `-u` | — | Username (prompted if omitted) |
| `--pat` | — | — | Personal Access Token (see [Authentication](#authentication)) |
| `--allow-insecure-http` | — | false | Allow plain HTTP for non-localhost servers (testing only) |

---

## Commands

### `init`

Initialise a local directory for syncing. Authenticates with the server, fetches the current manifest, and writes `.agblogger-manifest.json` to `--dir`.

```bash
agblogger --dir ./my-blog --server https://your-server.com init
```

Run this once per machine/directory before using `status` or `sync`.

---

### `status`

Show what a sync would do without making any changes. Safe to run at any time.

```bash
agblogger --dir ./my-blog status
```

Output lists files in four categories:

| Category | Meaning |
|---|---|
| `to_upload` | Local files newer than the server copy |
| `to_download` | Server files not present locally (or server version newer) |
| `to_delete_local` | Files deleted on the server since last sync |
| `to_delete_remote` | Files deleted locally since last sync |
| `conflicts` | Files changed on both sides since last sync |

---

### `sync`

Perform a bidirectional sync. By default, displays the plan (same as `status`) and asks for confirmation before making any changes.

```bash
agblogger --dir ./my-blog sync
```

Skip the confirmation prompt:

```bash
agblogger --dir ./my-blog sync --yes
```

#### Conflict resolution

When the same file has been modified both locally and on the server since the last sync, the **server version wins**. The CLI saves your local version as `<filename>.conflict-backup` before overwriting.

{: .warning }
Conflict backups are plain files — they are not tracked by the manifest and will not be uploaded automatically. Review them and merge any changes you want to keep before deleting them.

---

## Authentication

The CLI tries authentication methods in this order:

1. **`--pat` flag** — a Personal Access Token passed on the command line (one-time use, never stored)
2. **`AGBLOGGER_PAT` environment variable** — a PAT stored in your shell environment
3. **Interactive login** — prompted for username and password; the session token is stored securely for subsequent commands

### Creating a Personal Access Token

1. Log in to your AgBlogger instance
2. Open **Settings → Access Tokens**
3. Click **New token**, give it a name, and copy the value

Use the token with the `--pat` flag:

```bash
agblogger --dir ./my-blog --pat agb_xxxxxxxxxxxx sync
```

Or export it for all commands in the current shell:

```bash
export AGBLOGGER_PAT=agb_xxxxxxxxxxxx
agblogger --dir ./my-blog sync
```

---

## Typical workflow

```bash
# One-time setup: initialise the local directory
agblogger --dir ~/blog --server https://your-server.com init

# Edit posts locally
$EDITOR ~/blog/posts/2025-01-my-post.md

# Preview what will change
agblogger --dir ~/blog status

# Push local edits and pull any server changes
agblogger --dir ~/blog sync
```

---

## The manifest file

`agblogger init` and `agblogger sync` maintain a file called `.agblogger-manifest.json` in the content directory. It records the SHA-256 hash of every file at the time of the last successful sync. The CLI uses this as the "common ancestor" when computing the three-way merge plan.

{: .note }
Do not edit or delete `.agblogger-manifest.json` by hand. If it gets out of sync, re-run `agblogger init` to rebuild it from the server's current state.
