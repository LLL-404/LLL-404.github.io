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

One URL in, one clean file out. Four pipelines — plain HTTP, headless browser, CAPTCHA solver, and real Firefox — ordered by how hard the target fights back. Output as Markdown, plain text, or structured JSON. When a site rebuild breaks your selectors, a stored element fingerprint brings the same data back.

## What this solves

The most easily overlooked fact in scraping: **the status code can be 200 while the content you got back is completely empty.**

| Page type | Result from a plain HTTP request | What it actually needs |
|---|---|---|
| Static HTML | Full data, 0.75s | Plain HTTP is enough; a browser is wasted effort |
| JS-rendered | HTTP 200, **0 items** — an empty shell | A real browser must render it |
| Access-restricted (CAPTCHA etc.) | **403**, only 58 characters: "Just a moment…" | A solver pipeline |

So this tool does not chase one universal fetch method. It puts four pipelines side by side so you can **pick by how hard the target defends itself** — and it turns "is this actually the real content?" into a verifiable check instead of a guess based on the status code.

`http` plain request · fastest | `browser` headless · runs JS | `stealth` patched kernel · beats CAPTCHAs | `firefox` Firefox kernel · different fingerprint

Defense difficulty rises left to right, and so does latency. All four pipelines were tested end-to-end against the same set of targets, each with its raw output archived.

## Capability mapping

| Common scraping-job requirement | The evidence in this project |
|---|---|
| Write scrapers in Python | A single-file CLI `scrape.py` (224 lines): parameterized selectors, list-item + field extraction, output format chosen by file extension, fully type-annotated |
| Understand network data-fetching and handle access restrictions across sources | Each pipeline's boundary was drawn by a controlled experiment: a JS page yields 0 items over HTTP but 10 in a browser; a CAPTCHA page returns 403 with 58 characters over HTTP but 200 with 1588 characters via the stealth pipeline. It also rules out one problem that lives outside the browser entirely (see the debugging case below) |
| Data cleaning and structured conversion | Three output forms on demand: **Markdown body / plain text / structured JSON**; `-i` names the list item and `-f` the fields, extracting a whole batch in one pass |
| Scrape operations, source registry, and convention-building | An **adaptive fingerprint store** (SQLite): `--learn` saves an element's fingerprint once, and `--relocate` recovers the data after a site rebuild; the store is partitioned by registrable domain, so the same site reconnects across a redesign |

## Measurements

All measured on this machine, with artifacts archived.

**1. JS-rendered pages need a browser** — the most telling comparison:

| Pipeline | Status | Items found | Elapsed |
|---|---|---|---|
| Plain HTTP (spoofed headers) | 200 | **0** | 0.73 s |
| Headless browser render | 200 | **10** | 2.71 s |

The status codes are identical. Only counting items reveals that the HTTP run got an empty shell.

**2. The stealth pipeline does beat CAPTCHAs**:

| Pipeline | Result | Body | Elapsed |
|---|---|---|---|
| Plain HTTP | **403** | 58 chars (interstitial) | fast |
| Stealth (auto-solves the challenge) | **200** | 1588 chars (real content) | 11.4 s |

**3. Selector self-healing is real** — renaming all 10 `class="quote"` occurrences to `class="card item"`, equivalent to a CSS refactor:

| Approach | Matches | Result |
|---|---|---|
| Original selector after the rebuild | **0** | — (proves the rebuild really did break it) |
| Same page + adaptive recovery | **10** | Recovered text is byte-identical to the pre-rebuild version |

**4. All four pipelines pass** (re-run):

| Pipeline | Target type | Result |
|---|---|---|
| `http` | Static list page | 200 · 1.5 s · 20 items |
| `browser` | JS-rendered page | 200 · 4.0 s · 10 items |
| `stealth` | Access-restricted page | 200 · 16.2 s · 26,565 chars of Markdown |
| `firefox` | Same JS-rendered page (different kernel) | PASS · 5.1–6.5 s · 10 items · kernel Firefox/155.0 |

## A debugging case worth telling

**Symptom**: for the same page, a plain request returned 200 in 1.6s and the Chromium pipeline returned 10 items in 4s — but switching to the Firefox kernel produced a **120-second timeout with a constant 0 items**. It looked like "Firefox just doesn't work here."

**The actual chain**: this machine's browser kernel version introduced a local-network access gate → one external font request in the page's stylesheet was denied → that request hung → the stylesheet never finished → a script placed later in the page must, per spec, wait for the stylesheet → the load event never fired → the selector had nothing to match.

Rather than touching system settings, I **isolated the single switch with a one-variable A/B**:

| Group | Change | Result |
|---|---|---|
| A · default | none | **25 s timeout** · 0 items · page stuck loading |
| B · only that switch off | one browser preference | **3.0 s return** · 10 items · healthy state |

A fallback switch is also kept: any subresource resolving to an internal address is aborted outright, so it fails fast instead of hanging. Both fixes run against the same page yield 10 items with field-for-field identical data.

What this case demonstrates is not "knows a particular library" but: **following "the tool errored" all the way down to "browser kernel behavior plus page-loading spec," then confirming causation with a single-variable experiment.**

## Engineering notes and trade-offs

**What went right**

- **Criteria first**: every capability gets a reproducible check script, and conclusions wait for the numbers
- **Offline-verifiable**: fetched HTML can be re-run as input, so results can be rechecked without a network
- **Artifacts archived**: 44 raw outputs (logs / JSON / Markdown) ship with the project, so conclusions trace back
- **Honest boundaries**: a dedicated "what I did not do" section lists unverified capabilities rather than overstating

**Pitfalls hit and written down**

- The fingerprint store's domain key normalizes to the registrable domain; testing against local files with a mismatched key silently returns 0 matches
- The upstream library hardcodes Chromium for its fetchers, so a different kernel means wiring your own pipeline
- Element-level Markdown conversion does not exist; you have to fall back yourself
- The library's configuration is **process-global state**, so mixed multi-site scraping must be handled per site

Environment: Python 3.13 with a uv virtualenv; browser kernels reused from builds already on the machine rather than downloaded again.

## Notes

Every number on this page comes from artifacts inside the project and can be checked item by item. It contains no real account credentials and no personal information. The target sites used for demos and experiments are public scraping-practice sites and public demo pages. The public version contains no site-specific implementation for any real business target.
