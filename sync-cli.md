---
layout: default
title: Sync Tool
nav_order: 3
---

# Sync Tool
{: .no_toc }

The AgBlogger sync tool lets you write and manage your posts as ordinary text files on your computer. Whenever you're ready to publish, one command pushes your changes to your blog — and pulls down anything you wrote via the web.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Why use the sync tool?

- **Write offline** — draft posts in your favourite text editor without needing an internet connection
- **Keep a local backup** — your posts live as real files on your hard drive, not just on a remote server
- **Use any editor** — Obsidian, iA Writer, VS Code, Notepad — anything that saves plain text files works

---

## Installation

Download the `agblogger` program for your operating system from the [releases page](https://github.com/agblogger/agblogger/releases). No installation required — just save it somewhere convenient and you're ready to go.

---

## Getting started

Run this once to connect the sync tool to your blog and choose a folder on your computer to store your posts:

```bash
agblogger --dir ~/blog --server https://your-server.com init
```

Replace `~/blog` with any folder you'd like to use, and `https://your-server.com` with your blog's address. You'll be asked to sign in.

---

## Your everyday workflow

**1. Write a post**

Create or edit a `.md` file in your posts folder using any text editor.

**2. Check what's changed**

See a preview of what will be uploaded or downloaded before anything actually happens:

```bash
agblogger --dir ~/blog status
```

**3. Sync**

Push your local edits to the blog and pull down any changes made via the web:

```bash
agblogger --dir ~/blog sync
```

The tool will show you what it's about to do and ask for confirmation. To skip the confirmation step:

```bash
agblogger --dir ~/blog sync --yes
```

---

## What happens if the same post is edited in two places?

If you edit a post on your computer and also edit it via the web before syncing, there's a conflict. AgBlogger will flag the conflicting file in the sync output so you can review it and decide what to keep before syncing again.

---

## Signing in

The first time you run a command, you'll be prompted for your username and password. Your session is saved securely, so you won't need to sign in again.
