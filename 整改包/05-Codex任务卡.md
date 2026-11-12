# Codex 任务卡（交给李少春执行）

## 每张卡都必须遵守的安全规则（先贴给 Codex）

```
你在修改 OneLaser 的 Shopify 主题。规则：
1. 只改【复制出来的主题】，主题名格式：OL-YYYYMMDD-任务编号（例 OL-20261203-A1）。绝不直接改 Live 主题。
2. 改完给出预览链接 (?preview_theme_id=...) 和改动文件清单，等人工确认后才能发布。
3. 不删除任何 section/snippet/asset 文件，只改内容；要删的先改名加 _old 后缀。
4. 不改价格、库存、结账、插件(app)设置。
5. 每次发布前，把当前 Live 主题再复制一份作为回滚备份。
```

黑五/网一冻结期（11/21–12/2）：只允许做 A 组里纯文字的卡（A1、A2），其他一律等 12/3 之后。

---

## A 组：快速修错（低风险，先做）

### A1 · 修 H1 里的 “Image banner”
- **给 Codex 的指令**：在主题里搜索 `Image banner`，找到把这段无障碍文字放进 `<h1>` 的区块模板(多半是 image-banner 类 section)，把它移出标题(改成 `aria-label` 或 `visually-hidden` 的 span 放在 h1 外)。
- **验收**：/pages/contact-us、/pages/laser-engraving-community、/pages/wood-laser-engraving-cutting-ideas 三页 H1 文字不再带 “Image banner”。

### A2 · 按《03-文案修正.csv》改标题 / H1 / Meta
- 这张卡主要在 Shopify 后台手工完成(页面编辑 → 搜索引擎优化)，H1 由区块设置的标题字段控制。
- 分类页的 “Collection:” 前缀：让 Codex 在 `sections/main-collection-banner.liquid`(或同名区块) 里删掉 `{{ 'sections.collection_template.title' | t }}:` 之类的前缀。
- **验收**：表中每一行在线上核对一遍，状态改成“已完成”。

### A3 · 导航菜单统一（12/3 之后）
- 按《04-导航菜单.md》在后台新建 `main-menu-2026`；让 Codex 把页头和手机抽屉(menu-drawer)都改成读取同一个菜单 handle。
- **验收**：桌面与手机菜单链接完全一致；Get a Quote → /pages/sales-consultation；Become Affiliate → /pages/partners。

### A4 · 301 重定向第一批（12/3 之后）
- 顺序很重要：Shopify 只有在原页面**不存在**时重定向才生效。
  1. 政策页：先把 /pages/xxx-policy 的正文复制到 设置 → 政策 对应栏目，核对一致；
  2. 其他页面：确认内容已并入目标页(见 C3/C4)；
  3. 把原页面/分类改为“隐藏”或删除；
  4. 后台 → 导航 → URL 重定向 → 导入《01-redirects-第一批.csv》。
- 黑五博客 4 篇先指向 /pages/bfcm26；12/3 起把 /pages/bfcm26 本身再 301 到 /collections/limited-offers，并把这 4 条改成直接指向 limited-offers(避免两跳)。
- **验收**：用浏览器逐条打开旧 URL，全部 301 到目标且无 404；Google Search Console 重新提交 sitemap。

---

## B 组：字体与风格统一

### B1 · 引入统一样式文件
- 把《02-onelaser-unified.css》上传为 `assets/onelaser-unified.css`，在 `layout/theme.liquid` 的 `</head>` 前、所有其他样式之后加：
  `{{ 'onelaser-unified.css' | asset_url | stylesheet_tag }}`
- **注意**：Claude 已在线上 XRF 页实测注入本文件——6 个字重都能正确加载，但页面上的标题**不会变**，因为各个区块用更具体的选择器写死了字体和字号。所以 B1 只是打底，真正统一要靠 B2 和 B4 改区块代码。不要为了“立刻见效”往这个文件里加 `!important`。
- **验收**：浏览器控制台 `document.fonts` 里能看到 Certia 400–900 六个字重都是 loaded；新做的区块只用 `var(--ol-…)` 变量。

### B2 · 把写死的旧字体名换成字重
- **给 Codex 的指令**：在 `sections/`、`snippets/`、`templates/*.json`、`assets/*.css` 中，把
  `font-family: Certia-Medium` → `font-family: var(--ol-font); font-weight: 500`
  `Certia-SemiBold` → 600，`Certia-Bold` → 700，`Certia-ExtraBold` → 800，`Certia-Black` → 900，
  `Archivo`、`Poppins` → `var(--ol-font)`。列出每个改动文件和替换次数。
- 全部替换完、预览检查无误后，再删掉 base.css 里旧的 6 个 @font-face。
- **验收**：浏览器控制台运行
  `[...new Set([...document.querySelectorAll('body *')].map(e=>getComputedStyle(e).fontFamily.split(',')[0]))]`
  只剩 Certia(以及客服插件自带字体)。

### B3 · 第三方插件字体
- 在后台逐个改插件设置：表单插件字体 Almarai → 继承主题；UpPromote/联盟推荐条 Poppins、Francois One → 继承；Tidio 聊天保持默认。

### B4 · 字号与颜色清理
- **给 Codex 的指令**：在区块里搜索写死的 `font-size: 10px|11px`，改为最小 12px；搜索 `#e7310e` 用在按钮背景的地方改为 `var(--ol-action-red)`；搜索按钮 `border-radius`，统一为 `var(--ol-radius-pill)`。
- 区块里的标题字号也要改：H1/H2/H3 的 `font-size` 写死值改为 `var(--ol-h1)`、`var(--ol-h2)`、`var(--ol-h3)`(XRF 页现在有 12 种标题字号)。
- **验收**：XRF 页标题字号种类 ≤4(H1、H2、H3、卡片小标题)，正文字号种类 ≤6，按钮圆角只剩 999px。

---

## C 组：页面合并与重做（由 Claude 出方案/代码，李少春上架）

| 卡 | 内容 | Claude 交付 | 李少春做 |
|---|---|---|---|
| C1 | 机器对比页重做(全系参数表 + 3 题选机) | 完整 section 代码 + 文案 | 上传、填参数、预览 |
| C2 | 修正对比页的错误声明(价格、速度、保修、Made in USA) | 改好的文案 | 老板确认 Made in USA 措辞后上线 |
| C3 | Support Hub：/pages/troubleshooting 收拢 9 个入口 | 页面结构 + 文案 | 搬运手册/固件内容 |
| C4 | See one in person：合并 3 个 Demo 页面 | 页面结构 + 文案 | 上架、维护 Demo Host 名单 |
| C5 | 统一产品页模板(≤12 模块)，XRF 先行 | 以 V4 原型为基础出模板 | 套用到 Cobra/Hydra/VertiGo |
| C6 | 首页改版(V4 原型落地 Shopify) | 区块代码 | 配图(ChatGPT)、上架 |
| C7 | 材料参数库(可筛选) | 数据结构 + 页面代码 | 整理参数表数据 |
| C8 | 博客主题簇合并 | 每簇合并稿 + 301 清单 | 后台合并、导入重定向 |
