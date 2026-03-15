---
layout: default
title: Setting Up Your Blog
nav_order: 2
---

# Setting Up Your Blog
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Before you begin

Setting up AgBlogger requires a server — a computer that stays on and connected to the internet so your blog is always accessible. Most people use a cheap cloud server (a "VPS") from a provider like DigitalOcean, Hetzner, or Linode, which typically costs a few dollars a month.

You'll also need:

- A **domain name** (e.g. `myblog.com`) pointing at your server — optional, but recommended for a clean web address
- **Docker** installed on the server — Docker is a tool that packages AgBlogger and everything it needs into a neat bundle; [installation instructions are on the Docker website](https://docs.docker.com/engine/install/)
- The AgBlogger files downloaded to your server:

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
```

{: .note }
Not sure about any of this? Ask a technically minded friend or hire a sysadmin to do the one-time setup. Once it's running, you won't need their help to write posts.

---

## Running the setup wizard

AgBlogger includes a setup wizard that asks you a few simple questions and configures everything automatically. Run it with:

```bash
just deploy
```

The wizard will ask you for:

| Question | What to enter |
|---|---|
| Admin username | The username you'll log in with |
| Admin display name | Your name as it appears on the blog |
| Admin password | A strong password for your account (at least 8 characters) |
| Your domain name | e.g. `myblog.com` (leave blank for local-only access) |
| Email address | Used to receive TLS certificate notices (optional but recommended) |

When it's done, start your blog:

```bash
docker compose --env-file .env.production up -d
```

Your blog is now running. Visit `https://your-domain/login` (or `http://localhost` if you skipped the domain) and sign in with the username and password you chose.

---

## Logging in for the first time

After the server starts, open your blog's address in a browser and sign in with the admin credentials you set during setup.

If you ever forget your password, you can reset it by stopping the server and restarting it — the admin account is recreated from the credentials you originally configured.

---

## Keeping things running

A few useful commands if you ever need to manage the server:

```bash
# See if everything is running
docker compose --env-file .env.production ps

# View the server log (useful for troubleshooting)
docker compose --env-file .env.production logs -f

# Stop the server
docker compose --env-file .env.production down

# Start it back up
docker compose --env-file .env.production up -d
```
