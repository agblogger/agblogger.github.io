---
layout: default
title: Deployment Guide
nav_order: 2
---

# Deployment Guide
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

- A Linux server
- [Docker](https://docs.docker.com/engine/install/) and Docker Compose
- [just](https://github.com/casey/just) command runner
- A domain name pointing at your server

---

## Setup wizard

`just deploy` runs an interactive wizard that generates `.env.production` with your configuration.

| Prompt | Details |
|---|---|
| Admin username | The username you'll log in with |
| Admin display name | Your name as it appears on the blog |
| Admin password | A strong password (at least 8 characters) |
| Domain name | e.g. `myblog.com` — leave blank for local-only access |
| Email address | For TLS certificate notices — only prompted if a domain is set |

Start the server after the wizard completes:

```bash
docker compose --env-file .env.production up -d
```

Visit `https://your-domain` and log in.

---

## Docker management

```bash
docker compose --env-file .env.production ps        # status
docker compose --env-file .env.production logs -f    # logs
docker compose --env-file .env.production down       # stop
docker compose --env-file .env.production up -d      # start
```

---

## Updating

From the `agblogger` directory on the server:

```bash
git pull
docker compose --env-file .env.production up -d --build
```

Your posts and settings are preserved across updates.

---

## Password reset

The admin account is created on first startup from the credentials in `.env.production`. Restarting the server does not reset the password.

To reset a forgotten admin password: update `ADMIN_PASSWORD` in `.env.production`, delete the admin user from the database, and restart the server.
