---
layout: home
title: Home
nav_order: 1
---

# AgBlogger

A self-hosted, Markdown-first blogging platform. Write in any editor, organize with labels, cross-post to social media, and sync between your computer and server.

---

## Quick start

### Deploy

Requirements: a Linux server with [Docker](https://docs.docker.com/engine/install/) and [just](https://github.com/casey/just) installed. A domain name is optional.

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
just deploy                                      # interactive setup wizard
docker compose --env-file .env.production up -d   # start the server
```

Your blog is live at `https://your-domain` (or `http://localhost` without a domain). Log in with the admin credentials you set during setup.

See the [deployment guide]({% link server-deployment.md %}) for Docker management, updates, and password resets.

### Sync tool

The sync tool keeps a local folder of Markdown files in sync with your blog. Download it from the [releases page](https://github.com/agblogger/agblogger/releases).

```bash
agblogger --dir ~/blog --server https://your-server.com init   # one-time setup
```

Write posts as `.md` files in the `posts/` folder:

```markdown
---
labels:
  - "#cooking"
---

# My First Post

Post content here. The title is extracted from the first `#` heading.
Dates are filled in automatically on sync.
```

Then sync:

```bash
agblogger --dir ~/blog status   # preview changes
agblogger --dir ~/blog sync     # push and pull
```

See the [sync tool guide]({% link sync-cli.md %}) for post format details, conflict handling, and authentication.

### Cross-posting

Connect social accounts under **Admin → Social** in the web UI, then cross-post from any published post's editor. See the [cross-posting guide]({% link cross-posting.md %}).

---

## Features

- **Write in Markdown** — posts are `.md` files you can create in any text editor, from Notepad to VS Code. Markdown is a way to format text using plain characters like `**bold**` and `# Heading`.
- **Organize with labels** — group your posts with labels and browse them in a visual label map.
- **Share to social media** — publish a post to Bluesky, Mastodon, X (Twitter), and Facebook in one click.
- **Search everything** — find any post instantly by searching its title, content, or labels.
- **Sync from your computer** — keep a folder on your computer in sync with your blog, so you always have a local backup. Works offline.
- **Invite collaborators** — share your blog with other writers using invite codes.
