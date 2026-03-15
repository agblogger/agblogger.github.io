---
layout: default
title: Server Deployment
nav_order: 2
---

# Server Deployment
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Prerequisites

- **Docker** and **Docker Compose** (v2) installed on the host
- A domain name pointing at the server if you want automatic HTTPS via Caddy
- [`just`](https://github.com/casey/just) task runner (`brew install just`, `cargo install just`, or grab a binary release)
- The AgBlogger repository cloned to the host:

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
```

---

## Interactive deployment

Run the deployment wizard. It asks a series of questions and writes all the config files you need:

```bash
just deploy
```

The wizard will prompt you for:

| Prompt | Description |
|---|---|
| `SECRET_KEY` | Auto-generated 64-char hex key, or paste your own |
| `ADMIN_USERNAME` | Username for the initial admin account |
| `ADMIN_PASSWORD` | Password for the initial admin account |
| `TRUSTED_HOSTS` | Comma-separated domains or IPs (e.g. `yourdomain.com`) |
| `TRUSTED_PROXY_IPS` | Optional — IP(s) of a reverse proxy in front of Caddy |
| Enable Caddy? | `yes` for automatic HTTPS; `no` for plain HTTP on a custom port |
| Expose publicly? | `yes` to bind Caddy to `0.0.0.0`; `no` for localhost-only |
| `HOST_PORT` | (No-Caddy mode only) Host port to bind the container to |

The wizard produces:

- `.env.production` — all runtime environment variables
- `Caddyfile.production` — Caddy reverse-proxy config (if Caddy enabled)
- `docker-compose.caddy-public.yml` — optional override for public Caddy exposure
- `docker-compose.nocaddy.yml` — optional override for no-Caddy mode

---

## Starting the server

### With Caddy (recommended)

**Localhost only** (e.g. behind your own reverse proxy or for local testing):

```bash
docker compose --env-file .env.production up -d
```

**Public internet** (Caddy binds to `0.0.0.0`, obtains Let's Encrypt certificate):

```bash
docker compose --env-file .env.production \
  -f docker-compose.yml \
  -f docker-compose.caddy-public.yml \
  up -d
```

### Without Caddy

```bash
docker compose --env-file .env.production \
  -f docker-compose.nocaddy.yml \
  up -d
```

The server listens on `http://0.0.0.0:<HOST_PORT>` — wire up your own reverse proxy if needed.

---

## Common operations

```bash
# Check running containers
docker compose --env-file .env.production ps

# View live logs
docker compose --env-file .env.production logs -f

# Stop the server
docker compose --env-file .env.production down

# Restart after a config change
docker compose --env-file .env.production up -d --force-recreate
```

---

## First login

Once the server is up, visit:

- **With Caddy**: `https://<your-domain>/login`
- **Without Caddy**: `http://localhost:<HOST_PORT>/login`

Log in with the `ADMIN_USERNAME` / `ADMIN_PASSWORD` you set during deployment. The admin account is created automatically on first startup.

{: .note }
To reset the admin password, stop the server, delete the database volume (`docker volume rm agblogger_db`), and restart. The admin account is recreated from the env variables.

---

## Creating invite codes

AgBlogger uses invite-based registration. After logging in as admin:

1. Open **Settings → Users**
2. Click **Generate invite**
3. Share the invite URL with new users

---

## Development server

For local development without Docker:

```bash
just setup          # Install deps, create .env, init local DB directory
just start          # Start backend + frontend (auto-picks free ports)
just health         # Check backend and frontend health
just stop           # Stop dev servers
```

Default dev credentials: `admin` / `admin` (configurable in `.env`).

| Service | URL |
|---|---|
| Frontend | `http://localhost:5173` |
| Backend API | `http://localhost:8000` |
| API docs | `http://localhost:8000/docs` |

Custom ports:

```bash
just start backend_port=9000 frontend_port=9173
```

Run behind a local Caddy instance (mirrors the production setup):

```bash
just start-caddy-local    # Starts everything; frontend at http://localhost:8080
just health-caddy-local   # Health check for the Caddy-backed stack
```
