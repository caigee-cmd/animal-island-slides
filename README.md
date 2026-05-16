# Animal Island Slides

> A [Claude Code](https://claude.com/claude-code) skill that turns a prompt — or a `.pptx` — into a polished, single-file HTML deck in *Animal Crossing: New Horizons* style.
>
> 一个 Claude Code 插件：把一句话指令或一份 `.pptx` 直接变成动物森友会风格的零依赖 HTML 演示文稿。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-orange)](https://docs.claude.com/en/docs/claude-code/plugins)

---

## Demo / 演示

<!-- TODO: 补 3 张成品截图（封面 / 卡片栅格 / 时间线） + 1 段 GIF -->

| Cover | Card Grid | Timeline |
| :---: | :---: | :---: |
| _coming soon_ | _coming soon_ | _coming soon_ |

---

## What is this?

**Animal Island Slides** turns a paragraph of intent — or an existing `.pptx` file — into a polished, zero-dependency, single-file HTML deck inspired by *Animal Crossing: New Horizons*.

- **One file, zero dependencies.** No npm, no framework, no build step. Open the `.html` in any browser.
- **Viewport-locked.** Every slide is `height: 100dvh; overflow: hidden`. Content that doesn't fit gets split into a new slide — never scrolled.
- **One opinionated theme, on purpose.** Most AI slide generators converge on the same Inter-on-white aesthetic. This one always looks like Animal Crossing, and that is the differentiator.

## 它要解决什么

通用的 AI 幻灯片生成器，产出常常长得都一样：Inter 字体、纯白背景、靛蓝渐变。这个项目反过来——锁死一套有性格的设计系统（动物森友会的羊皮纸色、岛屿绿、按钮按压感、飘动的树叶），让 Claude 只负责"内容编排 + 布局选型"，不再自由发挥审美。

结果：**每张幻灯片都是同一种性格，且都不像默认的 AI 产物。**

---

## Install / 安装

> Requires Claude Code. See [Claude Code plugins docs](https://docs.claude.com/en/docs/claude-code/plugins).

```bash
# 1) Add this marketplace inside any Claude Code session
/plugin marketplace add caigee-cmd/animal-island-slides

# 2) Install the skill
/plugin install animal-island-slides
```

Or clone manually into your plugins directory:

```bash
git clone https://github.com/caigee-cmd/animal-island-slides \
  ~/.claude/plugins/animal-island-slides
```

---

## Quick Start / 快速上手

In any Claude Code session, just ask:

```
帮我做一份产品发布会的幻灯片
```

or:

```
Make me a slide deck for a product launch
```

Claude will:

1. Ask 5 quick questions (purpose, tone, length, content, language).
2. Draft an outline (cover → bullets → grid → timeline → close) and confirm with you.
3. Generate a single `.html` file in your current working directory.
4. Open it in your browser.

To convert a `.pptx`:

```
把 ~/Downloads/Q3-review.pptx 转成动森风格
```

Speaker notes, embedded images and slide text are all preserved as input — Claude re-composes them into the native layout grammar instead of literally copying the original geometry.

---

## Features / 功能一览

- **16 layouts** — Cover, Card Grid, Bullets, Two Column, Quote + Checklist, Pull Quote, Big Stat Hero, Timeline, Comparison, Stat Strip, Process Flow, Image Hero, Code, NookPhone Showcase, FAQ, Section Divider.
- **Strict density caps.** Each layout has a maximum content cap; Claude auto-splits a new slide when you exceed it.
- **Reusable design tokens.** All colors, radii, shadows, and easing curves are CSS custom properties — change one variable, retheme the deck.
- **`.pptx` import.** `scripts/extract-pptx.py` pulls text, images, and speaker notes from any PowerPoint file.
- **Export to PDF.** `scripts/export-pdf.sh` uses Playwright to render every slide at 1920×1080 into a single PDF.
- **One-click deploy.** `scripts/deploy.sh` packages local assets and ships a Vercel preview URL.
- **Accessible by default.** `prefers-reduced-motion` support, keyboard navigation (← → Space), swipe gestures, ARIA labels.
- **Three modes.** *A:* generate from scratch · *B:* convert from `.pptx` · *C:* enhance an existing HTML deck without breaking density rules.

---

## Philosophy / 设计宣言

### Why not Inter, Roboto, or "modern minimal"?

Because that has become the default AI aesthetic — and every AI deck on the internet already looks like that. The opinionated constraint is the product. Specifically:

- **No emoji.** All icons are inline SVG.
- **No fixed `px` / `rem` font sizes.** Everything is `clamp(min, preferred, max)` so slides reflow on any viewport.
- **No scrollbars inside a slide.** If it does not fit, the content is split into a new slide.
- **No `animation:` keyframes for entrance effects.** Use `.slide.entered .element { transition }` driven by JS — browsers skip animations inside invisible parents, which silently breaks entry effects on every slide except the first.
- **One palette, one font stack, one shadow system.** No free-style improvisation.

If you want fifteen variants of "clean minimal," there are plenty of other generators. This one trades flexibility for identity.

---

## Directory Layout / 目录结构

The repository follows the [Claude Code plugin layout](https://docs.claude.com/en/docs/claude-code/plugins):

```
.
├── .claude-plugin/
│   ├── plugin.json              # Plugin manifest
│   └── marketplace.json         # Marketplace registration
├── skills/
│   └── animal-island-slides/
│       ├── SKILL.md             # Main orchestration (~180 lines, always loaded)
│       ├── html-template.md     # Tokens, layouts, base HTML (loaded in Phase 3)
│       ├── animation-patterns.md# Entry / loop animations (loaded on demand)
│       └── scripts/
│           ├── extract-pptx.py
│           ├── export-pdf.sh
│           └── deploy.sh
├── examples/                    # Generated sample decks (TODO)
├── docs/
│   └── screenshots/             # README assets (TODO)
├── README.md
└── LICENSE
```

> **Note:** the current working copy still has skill files at the repo root. Before publishing, move them under `skills/animal-island-slides/` so Claude Code's plugin loader can discover them.

---

## Roadmap

- [ ] Four seasonal sub-themes (spring blossom / summer coast / autumn harvest / winter snow), sharing tokens but differentiated by palette and decor.
- [ ] Inline edit mode — `contenteditable` + `localStorage` autosave + `Ctrl+S` export.
- [ ] Presenter view with speaker notes and a timer (press `P` to toggle).
- [ ] HTML validation script (`scripts/validate.sh`) to enforce the hard rules automatically.
- [ ] Generated example gallery under `examples/`.

---

## Contributing

Issues and PRs welcome. Two ground rules:

1. Do not add a layout without also adding its density cap to `html-template.md`.
2. Do not introduce fonts, colors, or shadows that are not part of the existing token system. Open an issue first if you want to extend the palette.

---

## License

[MIT](LICENSE) © `caigee-cmd`
