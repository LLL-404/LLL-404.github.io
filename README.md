<div align="center">

**[English](./README.en.md)** · **简体中文**

# LLL-404.github.io

**新粗野主义个人站**

`Hugo` · `中英双语` · `手写模板` · `GitHub Actions 自动部署`

</div>

> [!IMPORTANT]
> 构建工具、自动化与游戏——跑起来才算数。站点即名片：项目档案、简历、联系方式，中英双语。

## 🗂 站点结构

```
content/
  ├── zh/   中文内容
  │     ├── _index.md          首页
  │     ├── about/             关于
  │     ├── contact/           联系方式
  │     ├── projects/          项目档案 × 7
  │     │     adb-mcp-server · browser-agent · long-image-to-video
  │     │     tapline · trae-works · warden · web-scraper
  │     ├── resume/            简历 × 3（自动化 / MCP 工具链 / 游戏原型）
  │     └── posts/             博客
  └── en/   English content（与 zh 同构）

layouts/   手写模板（baseof / list / single / 404）
i18n/      en.toml · zh.toml 界面文案
hugo.toml  站点配置（双语路由）
```

## 🧱 技术构成

| 组件 | 选型 |
|---|---|
| 静态站点生成 | [Hugo](https://gohugo.io)（`hugo.toml` 驱动中英双语路由） |
| 模板 | 手写 Hugo layouts，零第三方主题 |
| 界面文案 | `i18n/` 双语字典 |
| 部署 | GitHub Actions（`.github/workflows/hugo-deploy.yml`）构建并发布到 GitHub Pages |

## ⌨️ 本地运行

```bash
git clone https://github.com/LLL-404/LLL-404.github.io.git
cd LLL-404.github.io
hugo server -D   # http://localhost:1313
```

部署是全自动的：push 到 main，Actions 构建发布，线上地址 [lll-404.github.io](https://lll-404.github.io/)。

## 📌 说明

> [!TIP]
> 内容的中英文一一对应（`content/zh/` ↔ `content/en/`），新增项目或简历篇目时两边同步补。

---

<div align="center">

**[🌍 个人站](https://lll-404.github.io/)** · **[GitHub](https://github.com/LLL-404)** · **[📧 邮箱](mailto:2350178663@qq.com)**

</div>
