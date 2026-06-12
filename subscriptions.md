---
layout: default
title: Subscriptions
nav_order: 5
---

# Email Subscriptions
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## How it works

Readers can subscribe to your blog by email. When you publish a post, it is automatically delivered to all subscribers, styled to match the web version.

Email delivery is handled by [Resend](https://resend.com). AgBlogger never stores subscriber email addresses — Resend keeps the contact list, sends the emails, and manages unsubscribes. Your server stores only the encrypted Resend API key and a history of sent broadcasts.

Subscribing uses double opt-in: a reader enters their address on the subscribe page, receives a confirmation email, and is added to the list only after clicking the confirmation link.

---

## Setup

You need a Resend account (the free tier works for small blogs):

1. Sign up at [resend.com](https://resend.com) and verify your sending domain under **Domains**
2. Create an API key with full access under **API Keys**

Then in AgBlogger:

1. Open **Admin → Subscriptions**
2. Paste the **Resend API key**
3. Set the **From email** (an address on your verified domain) and optionally a **From name**
4. Turn on **Enable subscriptions** and save

On first enable, AgBlogger automatically creates the contact list in Resend and — when your blog is served over HTTPS — registers an unsubscribe webhook so contacts who unsubscribe are permanently deleted from Resend.

{: .note }
If the webhook can't be registered (for example, the blog isn't reachable over HTTPS yet), subscriptions still work fully; the admin panel shows a warning that unsubscribed contacts won't be auto-deleted from Resend, and registration is retried on later settings saves.

Once enabled, a **Subscribe** button appears in the blog header, linking to the public subscribe page.

To verify everything works, use **Send test email** in the admin panel to send yourself a sample broadcast.

---

## Broadcasts

- **Automatic** — publishing a post (creating it as published, or flipping a draft to published) sends it to all subscribers in the background. Each post is broadcast automatically at most once, so editing or republishing won't email subscribers again.
- **Manual** — pick any published post in **Admin → Subscriptions** and send it explicitly. Manual sends bypass the once-per-post guard, so double-check the broadcast history first to avoid emailing the same post twice.
- **History** — the admin panel lists every broadcast attempt with its delivery status and any error, along with the current subscriber count.

---

## Email rendering

Broadcast emails mirror the on-site post styling: headings, links, syntax-highlighted code blocks, blockquotes, note callouts, tables, and footnotes are restyled with email-safe inline CSS. Relative links and images are rewritten to absolute URLs, and footnote/anchor links point to the post on your blog. Each email includes a view-online link and an unsubscribe link.

{: .note }
Email clients can't run the JavaScript used for math rendering on the web, so LaTeX formulas are embedded as images rendered by the external service `latex.codecogs.com` (with the raw TeX as alt text). Loading these images exposes the reader's IP address to that service, like any remote image in email.

---

## Privacy and GDPR

- **No subscriber data on your server** — email addresses live only in Resend; AgBlogger stores none, and the subscribe endpoint never reveals whether an address is already subscribed.
- **Deletion on unsubscribe** — with the webhook registered, unsubscribed contacts are permanently deleted from Resend.
- **Optional GDPR fields** — controller name, contact, privacy policy URL, and postal address can be set in the admin panel.
- **Built-in privacy policy** — if you haven't created your own privacy page, AgBlogger serves a generated policy covering the subscription data processing, linked discreetly from the site footer while subscriptions are enabled.

{: .important }
Under GDPR, you remain the data controller and Resend acts as your data processor. [Resend's DPA](https://resend.com/legal/dpa) is incorporated automatically when you accept their terms of service — review it if you have EU subscribers.
