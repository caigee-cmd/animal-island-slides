# Changelog

All notable changes to **Animal Island Slides** are documented here.

---

## [2.1.0] — 2026-05-27

### 新增 / Added

- **Scenic Utilities（场景组件）** — 三个跨布局可复用的结构组件：
  - `ac-plank`：木牌标题容器，把标题从"网页文字"变成"岛上告示牌"
  - `ac-prop`：场景道具占位，封面/章节页建立视觉构图
  - `ac-inv-strip`：底部物品栏，补足游戏 UI 层感

### 改进 / Improved

- **风格强制规则** — 新增"风格语法"文档节：明确了什么情况下一张幻灯片算"不够像 AC"（仅有圆角卡片+动森色但缺少具象场景锚点），给生成过程提供可判断的分界线，而非模糊的审美建议。
- 封面布局（Layout A）加入更强的风格要求：标题优先落进木牌，至少搭配 2 个场景道具；禁止"绿底 + 大字 + 按钮"的空洞封面。

---

## [2.0.0] — 2026-05-17

### 新增 / Added

- **7 个新布局** — v2 布局包，总布局数从 16 增至 23：
  - **Q — Island Scene**：全沉浸式章节开场，岛屿场景背景
  - **V — Color Wheel**：4–13 色片的蜂窝色盘，适合调色板/角色展示
  - **R — Dialogue Modal**：NPC 对话框样式，适合引用、问答、Tips
  - **U — Icon Constellation**：中心 + 4–8 颗卫星的能力图/功能地图
  - **S — Tabs Switcher**：可交互的标签页切换
  - **W — Collapse Stack**：手风琴折叠展开，适合 FAQ / 详细说明
  - **T — Settings Panel**：控件/规格参数展示页，模拟游戏设置界面

- **4 个季节子主题** — 在 `<body>` 上设置 `data-theme` 即可切换：
  - `spring`：樱花色系 + 飘落花瓣动效
  - `summer`：海岸蓝系 + 波浪与海鸥动效
  - `autumn`：丰收橙系 + 飘落落叶动效
  - `winter`：雪景冷白系 + 飘雪动效
  - 每套主题替换约 15 个 CSS 变量；动效为纯 CSS keyframes，尊重 `prefers-reduced-motion`

- **Ambient Tools（环境氛围工具）** — 可叠加在任意布局上的装饰层：
  - Time HUD：角落实时时钟，模拟游戏 UI
  - Game Cursor：AC 风格自定义鼠标指针
  - 5 种分隔线样式

- **内置 SVG 图标库** — 10 枚与 AC UI 风格一致的内联图标，无需引入外部字体或图标集。

### 改进 / Improved

- SKILL.md 更新了布局选型表，新增 Phase 4 步骤 5：说明何时追加交互/环境 JS。

---

## [1.0.0] — 2026-05-16

### 新增 / Added

- 首次发布。
- **16 个基础布局** — Cover、Card Grid、Bullets、Two Column、Quote + Checklist、Pull Quote、Big Stat Hero、Timeline、Comparison、Stat Strip、Process Flow、Image Hero、Code、NookPhone Showcase、FAQ、Section Divider。
- **零依赖单文件输出** — 生成的 `.html` 无需 npm / 构建步骤，浏览器直开。
- **视口锁定** — 每张幻灯片 `height: 100dvh; overflow: hidden`，内容超出自动分页，不产生滚动条。
- **`.pptx` 导入** — `scripts/extract-pptx.py` 提取文字、图片、备注，Claude 重排为原生布局语法。
- **PDF 导出** — `scripts/export-pdf.sh` 用 Playwright 把每张幻灯片渲染为 1920×1080 并合并成 PDF。
- **一键部署** — `scripts/deploy.sh` 打包本地资源并发布 Vercel 预览链接。
- **无障碍支持** — `prefers-reduced-motion`、键盘导航（← → Space）、滑动手势、ARIA 标签。
- **三种使用模式** — A: 从零生成 · B: 从 `.pptx` 转换 · C: 增强已有 HTML deck。

---

[2.1.0]: https://github.com/caigee-cmd/animal-island-slides/compare/v2.0.0...v2.1.0
[2.0.0]: https://github.com/caigee-cmd/animal-island-slides/compare/v1.0.0...v2.0.0
[1.0.0]: https://github.com/caigee-cmd/animal-island-slides/releases/tag/v1.0.0
