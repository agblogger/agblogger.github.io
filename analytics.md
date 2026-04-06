---
layout: default
title: Analytics
nav_order: 5
---

# Analytics
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## How it works

AgBlogger ships with a self-hosted [GoatCounter](https://www.goatcounter.com/) sidecar that runs inside your Docker deployment. Hits are recorded server-side when readers load posts and pages — no client-side JavaScript or cookies are involved. Bot and crawler traffic is filtered out automatically.

All data stays on your server. GoatCounter is only reachable on the internal Docker network and is never exposed to the internet.

{: .note }
Analytics is a soft dependency — your blog serves content normally even if the GoatCounter container is stopped or unhealthy.

---

## Dashboard

Open **Admin → Analytics** to access the dashboard. It includes:

- **Summary cards** — total page views and top page for the selected period
- **Views over time** — area chart of daily views and visitors
- **Top pages** — sortable table of paths by view count; click a row to see its referrer sources and a per-page views chart
- **Breakdowns** — horizontal bar charts for browsers, operating systems, languages, locations, screen sizes, and campaigns.

Use the date-range selector at the top to switch between 7-day, 30-day, and 90-day presets or pick a custom range.

---

## View counts on posts

You can optionally show a view count on each published post's public page.

1. Go to **Admin → Analytics**
2. Enable **Show views on posts**

When enabled, each published post displays its visitor count. Drafts and unpublished posts never expose counts.

---

## Settings

Two toggles are available under **Admin → Analytics**:

| Setting | Default | Effect |
|---|---|---|
| **Analytics enabled** | On | Master switch — disables all hit recording and hides stats when off |
| **Show views on posts** | Off | Displays visitor counts on public post pages when on |

{: .note }
The deployment can also disable analytics at the infrastructure level with the `ANALYTICS_ENABLED_DEFAULT` environment variable. Analytics are active only when both the deployment variable and the admin toggle are on.

---

## Privacy

- **No cookies** — visitor deduplication uses server-side IP and User-Agent matching
- **No client-side scripts** — hits are recorded by the backend, not the browser
- **Self-hosted** — all data lives on your server in a GoatCounter SQLite database; nothing is sent to third parties
- **Admin visits excluded** — requests from logged-in admin or editor sessions are not counted
- **Bot filtering** — known crawlers are detected and excluded automatically
