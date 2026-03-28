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
- [Python 3.14+](https://www.python.org/) and [uv](https://docs.astral.sh/uv/)
- [just](https://github.com/casey/just) command runner

**On your server:**

- Linux
- [Docker](https://docs.docker.com/engine/install/) with Docker Compose V2 (the `docker compose` subcommand)
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
| Admin username | The username you'll log in with |
| Admin display name | Your name as it appears on the blog |
| Admin password | Initial admin password, stored server-side in plaintext. Change after first login |
| Deployment mode | Choose **tarball** (ship as a file), **registry** (pull from container registry), or **local** (deploy locally) |
| Image reference | e.g. `agblogger:latest` — used to tag or pull the image |
| Caddy mode | Choose **bundled** (reverse proxy with automatic HTTPS), **external** (shared Caddy instance), or **none** (bring your own proxy) |
| Domain name | e.g. `myblog.com` — needed for automatic HTTPS |
| Email address | For Let's Encrypt certificate notices (optional but recommended) |

When the wizard finishes, it creates a `dist/deploy/` folder containing:

- `setup.sh` — a script that sets everything up on the server
- `.env.production.generated` — your credentials and configuration (renamed to `.env.production` on first run by `setup.sh`)
- Docker Compose and Caddyfile configs (with `.generated` suffix, renamed by `setup.sh` on the server)
- `DEPLOY-REMOTE.md` — server management commands for your configuration
- `goatcounter/` — entrypoint script for the analytics sidecar
- `content/` — empty seed directory for the content volume
- `VERSION` — version marker for upgrade tracking

---

## Step 2: Copy to the server

```bash
rsync -av dist/deploy/ user@your-server:~/agblogger/
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
2. Copy the new bundle to the server.
3. If the image tag changed, update `AGBLOGGER_IMAGE` in `.env.production` to match.
4. Run `bash setup.sh` again.

Your posts, database, and `.env.production` are preserved across updates — `setup.sh` never overwrites them. Compose and Caddy config files are backed up as `.bak` files before being replaced.

---

## Deploying from source on the server

If you prefer to keep the full source repository on the server instead of using a deployment bundle, you can run the wizard directly on the server:

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
just deploy                                      # choose "local" deployment mode
```

The wizard builds the Docker image, starts the containers, and confirms they're healthy — all in one step.

To update, pull the latest source and re-run the wizard:

```bash
git pull
just deploy
```

This approach requires Git, Python, uv, and just on the server, but avoids the copy step.

---

## Other deployment options

The default wizard configuration uses a **bundled Caddy** reverse proxy that runs alongside AgBlogger in the same Docker Compose stack and handles HTTPS automatically. The wizard also supports two alternative Caddy modes:

### External Caddy (shared reverse proxy)

If your server already runs multiple web services behind a single Caddy instance — or you plan to — choose the **external** Caddy mode in the wizard. Instead of running its own Caddy container, AgBlogger joins an existing shared Caddy instance via a Docker network. Each service drops a site snippet into the shared Caddy's `sites/` directory, so you manage one Caddy container for all your domains.

The setup script bootstraps the shared Caddy instance automatically if it isn't already running.

### No Caddy (bring your own reverse proxy)

If you already have nginx, Traefik, or another reverse proxy handling TLS, choose the **none** Caddy mode. AgBlogger's port is exposed directly (default `8000`) and you configure your existing proxy to forward traffic to it. No automatic HTTPS is provided in this mode.

For full details on all wizard options, non-interactive flags, environment variables, and the complete CLI reference, see the [upstream deployment guide](https://github.com/agblogger/agblogger/blob/main/DEPLOYMENT.md).

---

## Password reset

The admin account is created on first startup from the credentials in `.env.production`. Restarting the server does not reset the password.

To reset a forgotten admin password: update `ADMIN_PASSWORD` in `.env.production` and restart the server. The password is re-synced from the environment on each startup.
