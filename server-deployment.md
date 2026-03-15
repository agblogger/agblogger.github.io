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

Setting up AgBlogger requires a server. Most people use a cheap cloud server (a VPS) from a provider like DigitalOcean, Hetzner, or Linode, which typically costs a few dollars a month.

You'll also need:

- A **domain name** (e.g. `myblog.com`) pointing at your server
- **Docker** installed on the server
- The AgBlogger source repository

To download the files, run these commands on the server:

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
```

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
| Email address | For TLS certificate notices — only asked if you enter a domain name (optional but recommended) |

When it's done, start your blog:

```bash
docker compose --env-file .env.production up -d
```

Your blog is now running. Visit `https://your-domain/login` (or `http://localhost` if you skipped the domain) and sign in with the username and password you chose.

---

## Logging in for the first time

After the server starts, open your blog's address in a browser and sign in with the admin credentials you set during setup.

If you ever forget your password, ask whoever manages your server for help — they can reset it by updating the admin password in the server's configuration file (`.env.production`) and recreating the admin account.

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

---

## Updating AgBlogger

To update AgBlogger to the latest version, run these commands on the server from the `agblogger` directory:

```bash
git pull
docker compose --env-file .env.production up -d --build
```

This downloads the latest code, rebuilds the application, and restarts it. Your posts and settings are preserved.

---

## What's next?

Your blog is up and running. Here's what you can do now:

- **Write your first post** — log in and start writing from the web interface
- **[Set up the sync tool]({% link sync-cli.md %})** — write posts offline from your computer and sync them to your blog
- **[Connect social accounts]({% link cross-posting.md %})** — share posts to Bluesky, Mastodon, X, and Facebook
- **Invite other writers** — create invite codes from the admin panel so others can join your blog
