---
title: "web-scraper"
date: 2026-10-05
weight: 7
draft: false
description: "四条取页链路对抗不同强度反爬，选择器失效后还能靠特征库把数据找回来。"
tags: ["Python", "爬虫", "反爬对抗"]
category: "工具"
tech_stack: ["Python", "Scrapling", "Playwright"]
---

一个 URL 进，一份干净文件出。纯 HTTP、无头浏览器、过盾、真 Firefox 四条链路按反爬强度递增，输出 Markdown / 纯文本 / 结构化 JSON 任选。选择器改版失效后，靠存过的元素指纹把同一批数据原样找回。

关键结论都来自本机对照实验：

- **状态码骗人**：同一个 JS 渲染页，纯 HTTP 与无头浏览器**都返回 200**，前者拿到 **0 条**、后者 **10 条** —— 只看状态码完全分辨不出来。
- **Cloudflare 能过**：纯 HTTP 403、正文 58 字（`Just a moment...`）；`StealthyFetcher` 解 turnstile 后 200、正文 1588 字。
- **改版自愈是真的**：把页面里 10 处 `class="quote"` 改成 `class="card item"` 模拟重构后，普通查询命中 0 个，`adaptive=True` 命中 10 个且文本逐字一致。

排障记录：本机 Firefox 打目标页曾 **120 秒超时、恒定 0 条**，而 curl 同页 1.6 秒 200。根因不在 Scrapling，而是 hosts 劫持 → CSS 里 `@import` 指向被按到回环的字体域 → **Firefox 155 的 Local Network Access 拒绝公网页面访问回环地址**，自动化里没人能点"允许"，请求挂死导致样式表没完、注入脚本不发、`.quote` 恒为 0。单变量 A/B 验证 `network.lna.enabled=False` 一个偏好即可解开：25.0s 超时 → **3.0s 返回、10 条**。Chromium 无此闸，所以同页正常。
