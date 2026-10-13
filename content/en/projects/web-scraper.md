---
title: "web-scraper"
date: 2026-10-05
weight: 7
draft: false
description: "Four fetch pipelines for different anti-bot levels, with selector self-healing after a site rebuild."
tags: ["Python", "Scraping", "Anti-bot"]
category: "Tool"
tech_stack: ["Python", "Scrapling", "Playwright"]
---

One URL in, one clean file out. Four pipelines — plain HTTP, headless browser, Cloudflare solver, and real Firefox — ordered by how hard the target fights back. Output as Markdown, plain text, or structured JSON. When a site rebuild breaks your selectors, a stored element fingerprint brings the same data back.

The findings come from controlled experiments on this machine:

- **Status codes lie**: for the same JS-rendered page, both plain HTTP and a headless browser return **200** — but HTTP gets **0 items** and the browser gets **10**. The status code alone tells you nothing.
- **Cloudflare is passable**: plain HTTP gets 403 with 58 characters of body (`Just a moment...`); `StealthyFetcher` solves the turnstile, returns 200 with 1588 characters.
- **Selector self-healing is real**: renaming all 10 `class="quote"` occurrences to `class="card item"` to simulate a CSS refactor drops a normal query to 0 hits; `adaptive=True` recovers all 10 with byte-identical text.

Debugging note: on this machine Firefox once **timed out at 120s with a constant 0 items** against a target page, while curl fetched the same page in 1.6s with a 200. The cause was not Scrapling — a hosts-file hijack pointed a font domain at loopback, a CSS `@import` referenced it, and **Firefox 155's Local Network Access blocks public pages from reaching loopback addresses**. No one can click "allow" in an automated session, so the request hung, the stylesheet never finished, the injecting script never ran, and `.quote` stayed at 0. A single-variable A/B confirmed that one preference fixes it: `network.lna.enabled=False` took it from **25.0s timeout to 3.0s with 10 items**. Chromium has no such gate, which is why the same page worked there.
