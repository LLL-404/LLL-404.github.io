# 个人主页 — Code Wiki

> Hugo 静态个人主页「LLL-404」，线上 https://lll-404.github.io/ （仓库 `LLL-404/LLL-404.github.io`，2026-11 由 personal-homepage 改名）。
> 本文 2026-11-14 全量重写。此前 7 月版描述的是旧设计（Playfair/GSAP/Three.js 体系），已随重构整体失效。

## 一句话架构

Hugo（无主题、手写模板）+ 双语 content（zh 默认在根、en 在 `/en/`）+ 全站文案走 `i18n/*.toml`；CSS 全部内嵌在 `layouts/partials/head.html`，JS 全部内嵌在 `layouts/partials/scripts.html`，**零运行时依赖**（无 GSAP/Three.js、无 npm）；GitHub Actions 构建部署到 Pages。

## 目录速查

```
hugo.toml                  # 站点配置：双语（label/locale/contentDir）、params
content/{zh,en}/           # 五个 section：projects/posts/resume/about/contact，Frontmatter + Markdown
i18n/{zh,en}.toml          # 全站文案键（nav_*、hero_*、filler_* 等）
layouts/
├── _default/baseof.html   # 骨架：<html lang>、字体、header→main→footer→scripts，含按钮文案首帧同步脚本
├── _default/{list,single}.html
├── index.html             # 首页：hero/作品集/足迹；网格空槽自动插虚线补位卡（mod len 3）
├── 404.html               # 404 页 + 可玩打砖块 canvas#bk
└── partials/
    ├── head.html          # 全站 CSS（Neo-Brutalism v5）+ DARK MODE 变量块 + 防闪白脚本
    ├── header.html        # 跑马灯 + 导航（relLangURL）+ 暗色开关 ×2
    ├── footer.html
    └── scripts.html       # 全站 JS：渐入/移动菜单/主题切换/卡片 3D 倾斜/打砖块
static/                    # 已清空（admin 2026-11-14 删除、jobs 已撤场）
.github/workflows/hugo-deploy.yml  # hugo_extended v0.169.0，push main 触发
```

> 注：hugo.toml 里 `[[languages.*.menu.main]]` 是死配置——header.html 用硬编码链接 + i18n，不读菜单。留着无害，以后统一或删除。

## 设计语言

Neo-Brutalism v5：`--ink`/`--paper` 两变量驱动，3px 黑边 + 硬偏移阴影，四荧光色 `--y --pk --cy --gn`。暗色主题只换 ink/paper 两变量全站反色 + 彩色底深字清单（`[data-theme="dark"]` 块，head.html 末尾）。字体：Archivo Black + Noto Sans SC + JetBrains Mono（Google Fonts）。

## 交互功能（scripts.html）

- **主题开关**：`data-theme-btn`，localStorage `theme` + `prefers-color-scheme` 兜底，`themechange` 自定义事件广播（404 画布跟着换色）
- **卡片 3D 倾斜**：`.wc/.jc` 鼠标跟随 ±6°，`hover:fine` + `reduced-motion` 双 matchMedia 门
- **404 打砖块**：键盘（←→/Space，焦点落在链接/按钮上时不拦）+ 触屏拖，3 命/计分/逐砖加速/切后台自动暂停
- **滚动渐入**：IntersectionObserver；移动端 burger 菜单

## 部署与门禁

- push main → Actions（hugo extended 0.169.0，`--gc --minify`）→ `deploy-pages@v4`
- 本地验证三道门（改版必跑）：
  1. python 抽内联 `<script>` → `node --check`
  2. `hugo -d <temp>` 构建：退出码 0 + grep 产物标记 + 零 WARN
  3. Edge headless `--screenshot`（`--force-dark-mode` 测暗色、`--virtual-time-budget` 防挂）
- 本地 hugo 在 `%TEMP%\hugo169\hugo.exe`（重下：`gh release download v0.169.0 -R gohugoio/hugo`）

## 历史备忘

- 2026-11-05 仓改名 personal-homepage → LLL-404.github.io（URL 从子路径变根域名）
- 2026-11-13 `git filter-repo` 剥除 static/jobs（曾误挂 4176 条岗位数据，数据备份在仓外 `散件归档/`）并强推；GitHub 对旧 SHA 的悬空缓存待 GC
- 2026-11-14 删除 static/admin（Decap CMS）——GitHub Pages 无 OAuth 代理，线上登录本就必失败

## 已知未办

- hugo.toml 死菜单配置（不渲染，无害）
- 旧悬空提交的 GitHub 缓存：等 GC 或联系 support 清理
