---
layout: home
title: Home
nav_order: 1
---

# AgBlogger

A self-hosted, Markdown-first blogging platform. Write in any editor, organize with labels, cross-post to social media, and synchronize between your computer and server.

---

## Features

- **Write in Markdown** — posts are [Markdown](https://www.markdownguide.org/getting-started/) `.md` files you can create in any text editor. AgBlogger provides a convenient Web editor with instant preview.
- **Organize with labels** — group your posts with labels and browse them in a visual label map.
- **Share to social media** — publish a post to Bluesky, Mastodon, X (Twitter), and Facebook in one click.
- **Search everything** — find any post instantly by searching its title, content, or labels.
- **Sync from your computer** — keep a folder on your computer in sync with your blog, so you always have a local backup.
- **Track analytics** — see view counts on posts and browse traffic stats in the admin dashboard.

---

## Quick start

### Deploy

Requirements: [just](https://github.com/casey/just) and [uv](https://docs.astral.sh/uv/) on your computer, [Docker](https://docs.docker.com/engine/install/) on your server.

```bash
git clone https://github.com/agblogger/agblogger
cd agblogger
just deploy                                        # interactive setup wizard
rsync -av dist/deploy/ user@your-server:~/agblogger/ # copy bundle to server
```

Then on the server:

```bash
cd ~/agblogger
bash setup.sh    # starts everything, including HTTPS
```

Your blog is live at `https://your-domain`. Log in with the admin credentials you provided during setup.

See the [deployment guide]({% link server-deployment.md %}) for managing the server, updates, and alternative deployment methods.

### Sync tool

The sync tool keeps a local folder of Markdown files in sync with your blog. Download it from the [releases page](https://github.com/agblogger/agblogger/releases).

```bash
agblogger init --dir ~/blog --server https://your-server.com   # one-time setup
```

Write posts as `index.md` files in dedicated post folders inside the `posts/` folder, e.g., in `posts/2026-03-11-my-first-post/index.md`:

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
agblogger status --dir ~/blog   # preview changes
agblogger sync --dir ~/blog     # push and pull
```

See the [sync tool guide]({% link sync-cli.md %}) for post format details, conflict handling, and authentication.

### Cross-posting

Connect social accounts under **Admin → Social** in the web UI, then cross-post from any published post's editor. See the [cross-posting guide]({% link cross-posting.md %}).
