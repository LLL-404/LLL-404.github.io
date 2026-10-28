<div align="center">

**English** · **[简体中文](./README.md)**

# LLL-404.github.io

**Neo-brutalist personal site**

`Hugo` · `bilingual (zh/en)` · `hand-written templates` · `GitHub Actions deploy`

</div>

> [!IMPORTANT]
> Build tools, automation, and games — if it runs, it counts. The site is the business card: project profiles, résumé, contact — in both Chinese and English.

## 🗂 Site structure

```
content/
  ├── en/   English content
  │     ├── _index.md          home
  │     ├── about/             about
  │     ├── contact/           contact
  │     ├── projects/          project profiles × 7
  │     │     adb-mcp-server · browser-agent · long-image-to-video
  │     │     tapline · trae-works · warden · web-scraper
  │     ├── resume/            résumé × 3 (automation / MCP tooling / game prototypes)
  │     └── posts/             blog
  └── zh/   中文内容（mirrors en/）

layouts/   hand-written templates (baseof / list / single / 404)
i18n/      en.toml · zh.toml UI strings
hugo.toml  site config (bilingual routing)
```

## 🧱 Stack

| Component | Choice |
|-----------|--------|
| Static site generation | [Hugo](https://gohugo.io) (`hugo.toml` drives the zh/en routing) |
| Templates | Hand-written Hugo layouts, zero third-party themes |
| UI strings | `i18n/` bilingual dictionaries |
| Deployment | GitHub Actions (`.github/workflows/hugo-deploy.yml`) builds and publishes to GitHub Pages |

## ⌨️ Run locally

```bash
git clone https://github.com/LLL-404/LLL-404.github.io.git
cd LLL-404.github.io
hugo server -D   # http://localhost:1313
```

Deployment is fully automatic: push to main, Actions builds and publishes. Live at [lll-404.github.io](https://lll-404.github.io/).

## 📌 Notes

> [!TIP]
> Chinese and English content mirror each other (`content/zh/` ↔ `content/en/`); when adding a project or résumé page, update both sides.

---

<div align="center">

**[🌍 Website](https://lll-404.github.io/)** · **[GitHub](https://github.com/LLL-404)** · **[📧 Email](mailto:2350178663@qq.com)**

</div>
