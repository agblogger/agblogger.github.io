---
layout: default
title: Deployment
nav_order: 2
---

# Deployment
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

You run an interactive setup wizard on your computer. The wizard asks for your admin credentials and domain name, then generates a deployment bundle — a folder with everything your server needs to run AgBlogger. You copy that folder to the server and run a single setup script.

---

## Prerequisites

**On your computer** (where you run the wizard):

- [Git](https://git-scm.com/)
- [Python 3.12+](https://www.python.org/) and [uv](https://docs.astral.sh/uv/)
- [just](https://github.com/casey/just) command runner

**On your server:**

- Linux
- [Docker](https://docs.docker.com/engine/install/) and Docker Compose
- A domain name pointing at the server (optional, but needed for HTTPS)

---

## Step 1: Generate the deployment bundle

Clone the repository on your computer and run the setup wizard:

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
just deploy
```

The wizard prompts you for:

| Prompt | Details |
|---|---|
| Deployment mode | Choose **registry** (pull from container registry) or **tarball** (ship as a file) |
| Admin username | The username you'll log in with |
| Admin display name | Your name as it appears on the blog |
| Admin password | A strong password (at least 8 characters) |
| Domain name | e.g. `myblog.com` — leave blank for local-only access |
| Email address | For TLS certificate notices — only prompted if a domain is set |

When the wizard finishes, it creates a `dist/deploy/` folder containing:

- `.env.production` — your credentials and configuration
- Docker Compose files
- `setup.sh` — a script that sets everything up on the server
- A `DEPLOY-REMOTE.md` with server-side management commands

---

## Step 2: Copy to the server

```bash
scp -r dist/deploy/ user@your-server:~/agblogger
```

---

## Step 3: Run the setup script

SSH into your server and run:

```bash
cd ~/agblogger
bash setup.sh
```

The script loads the Docker image, starts the services, sets up the reverse proxy (if configured), and waits for a health check to confirm everything is running.

Visit `https://your-domain` and log in.

---

## Managing the server

The bundle includes a `DEPLOY-REMOTE.md` with the exact management commands for your configuration. The general pattern is:

```bash
docker compose --env-file .env.production -f <compose-file> ps       # status
docker compose --env-file .env.production -f <compose-file> logs -f   # logs
docker compose --env-file .env.production -f <compose-file> down      # stop
docker compose --env-file .env.production -f <compose-file> up -d     # start
```

The compose file name depends on your setup (with or without Caddy, registry or tarball mode). Check `DEPLOY-REMOTE.md` for the exact commands.

---

## Updating

1. Pull the latest source on your computer and re-run `just deploy`.
2. Copy the new `dist/deploy/` to the server, **keeping your existing `.env.production`** (it contains your secrets and credentials).
3. Run `bash setup.sh` again.

Your posts and settings are preserved across updates. The setup script automatically backs up `.env.production` before each run.

---

## Deploying from source on the server

If you prefer to keep the full source repository on the server instead of using a deployment bundle, you can run the wizard directly on the server:

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
just deploy                                      # choose "local" deployment mode
docker compose --env-file .env.production up -d
```

To update, pull and rebuild:

```bash
git pull
docker compose --env-file .env.production up -d --build
```

This approach requires Git, Python, uv, and just on the server, but avoids the copy step.

---

## Password reset

The admin account is created on first startup from the credentials in `.env.production`. Restarting the server does not reset the password.

To reset a forgotten admin password: update `ADMIN_PASSWORD` in `.env.production`, delete the admin user from the database, and restart the server.
