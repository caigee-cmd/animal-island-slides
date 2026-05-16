---
name: animal-island-slides
description: 用动物森友会风格生成零依赖 HTML 演示文稿，或将 .pptx 文件转换为同款风格。当用户想做幻灯片、做 PPT、做演示文稿，或提到动物森友会/岛屿风格时触发。
---

# Animal Island Slides

生成零依赖、单 HTML 文件的演示文稿，设计风格来自 animal-island-ui（动物森友会 React 组件库）。

## 文件加载地图

下表中所有路径相对于 **skill 自身目录** `${CLAUDE_PLUGIN_ROOT}/skills/animal-island-slides/`（`CLAUDE_PLUGIN_ROOT` 由 Claude Code 在运行时自动注入，指向插件根目录）。读文件或执行脚本时，**必须**用 `${CLAUDE_PLUGIN_ROOT}/skills/animal-island-slides/<相对路径>` 的绝对形式，不要假设用户当前目录里有这些文件。

| 文件 | 用途 | 加载时机 |
|---|---|---|
| `SKILL.md` | 主流程和规则 | 始终（skill 调用时） |
| `html-template.md` | 色彩系统、布局 CSS/HTML 片段 | Phase 3（生成 HTML） |
| `animation-patterns.md` | CSS/JS 动画参考库 | Phase 3（生成 HTML，有入场动画需求时） |
| `scripts/extract-pptx.py` | PPT 内容提取 | Phase 4（转换模式） |
| `scripts/export-pdf.sh` | 导出 PDF | Phase 5（导出） |
| `scripts/deploy.sh` | 部署到 Vercel | Phase 5（分享） |

> 生成的 `.html` 输出文件依然写到**用户的当前工作目录**，不要写进插件目录。

---

## 核心原则

1. **零依赖** — 单 HTML 文件，内联所有 CSS/JS，无 npm，无框架
2. **视口适配（不可违反）** — 每张幻灯片严格 `height: 100dvh; overflow: hidden`，内容超出密度上限时自动拆分，绝不允许幻灯片内滚动
3. **风格统一** — 所有视觉决策遵循 html-template.md，不自由发挥颜色、字体或圆角
4. **无 emoji** — 图标一律用内联 SVG

---

## Phase 0：判断模式

用户发起请求后，判断属于哪种模式：

- **Mode A（新建）** — 从零开始创作 → 进入 Phase 1
- **Mode B（转换）** — 用户提供了 .pptx 文件 → 进入 Phase 4
- **Mode C（增强）** — 用户提供了已有 HTML 演示文稿要修改 → 进入 Phase 6

---

## Phase 1：内容收集（Mode A）

**用一次 AskUserQuestion 问完以下所有问题**，不要分多轮询问：

- **用途**（header: "用途"）：这份演示文稿是做什么的？
  选项：产品/项目汇报 / 教学课程 / 技术分享 / 团队内部
- **基调**（header: "基调"）：希望传达什么感受？
  选项：专业可信（沉稳） / 活力惊喜（有冲击力） / 轻松友好（愉快） / 清晰高效（干净利落）
- **篇幅**（header: "篇幅"）：大约需要几张幻灯片？
  选项：5-8 张（简短） / 10-15 张（中等） / 15 张以上（完整）
- **内容**（header: "内容"）：内容准备好了吗？
  选项：内容已准备好 / 有大纲/草稿 / 只有主题，需要帮我填充
- **语言**（header: "语言"）：幻灯片用什么语言？
  选项：中文 / English / 中英混排

如果用户选择"内容已准备好"或"有大纲/草稿"，请他们在回复中一并粘贴内容。

---

## Phase 2：规划大纲

根据收集到的内容，起草幻灯片大纲，格式如下：

```
第 1 张 [Cover]       封面 — 项目名称
第 2 张 [Divider]     第一章：背景
第 3 张 [Bullets]     现状分析（5 条要点）
第 4 张 [TwoColumn]   核心方案（左文右数据）
第 5 张 [Grid]        三大功能（3 张卡片）
...
第 N 张 [Cover]       结尾/感谢
```

**布局选择规则**：

| 内容类型 | 布局 |
|---|---|
| 封面、章节开场、结尾 | Cover |
| 章节过渡 | Section Divider |
| 纯文字、步骤、背景 | Text Bullets（≤5 条） |
| 功能列表、对比项、特性 | Card Grid（≤6 张卡片） |
| 核心观点 + 支撑要点 | Quote + Checklist（≤4 条） |
| 单独一句金句、客户原话、产品哲学 | Pull Quote |
| 左文右图、左文右数据 | Two Column |
| 单一关键数字 / 季度增长 / 里程碑数值 | Big Stat Hero |
| 时间线、路线图、里程碑序列（3-5 节点） | Timeline |
| 方案 A vs B、改造前后、新老对比 | Comparison |
| 3-4 个并列核心指标 / KPI 汇总 | Stat Strip |
| 业务流程 / 步骤 / Onboarding 链路（3-5 步） | Process Flow |
| 截图 / 产品照片 / 信息图（图片为主） | Image Hero |
| 代码片段 / 配置示例 / API 用法（技术分享） | Code Slide |
| 产品功能矩阵 / 章节总览 / 目录页（AC 标志性视觉） | NookPhone Showcase |
| 常见问题 / "为什么这么做" 复盘 / 技术 Q&A | FAQ |

用 AskUserQuestion 展示大纲并确认：
- 选项：看起来不错，开始生成 / 调整大纲

用户确认后进入 Phase 3。

---

## Phase 3：生成 HTML

**首先读取 `${CLAUDE_PLUGIN_ROOT}/skills/animal-island-slides/html-template.md`**；如果需要为幻灯片添加入场动画，同步读取 `${CLAUDE_PLUGIN_ROOT}/skills/animal-island-slides/animation-patterns.md`。然后：

1. 复制 BASE 代码作为文件骨架（title 替换为演示文稿名称）
2. 按大纲顺序组合各 LAYOUT 的 CSS 和 HTML 片段
3. 所有 `data-slide="N"` 从 0 开始顺序编号
4. 第一张幻灯片加 `class="slide ... active"`，其余只有 `class="slide ..."`
5. 将文件保存为 `[演示文稿名称].html`，路径在当前工作目录

**生成后**：
- 用 `open [文件名].html` 在浏览器中打开
- 告诉用户键盘操作方式（← → 翻页，Space 下一张）
- 询问是否需要调整或进入 Phase 5（导出）

### 生成时的检查清单（每张幻灯片逐一验证）

- [ ] `.slide` 有 `height:100dvh; overflow:hidden`
- [ ] 所有字号使用 `clamp()`
- [ ] 入场动画使用 `.slide.entered .element` 驱动 transition，**不使用** `animation:` 直接写在元素上（装饰性背景循环动画除外）
- [ ] 无 emoji，图标为内联 SVG
- [ ] 内容未超过该布局的密度上限，超出则已拆分
- [ ] `data-slide` 编号连续无间断

---

## Phase 4：PPT 转换（Mode B）

1. 确认用户已提供 .pptx 文件路径
2. 运行提取脚本（输出目录用 `.`，使提取内容落到用户当前目录）：
   ```bash
   python3 "${CLAUDE_PLUGIN_ROOT}/skills/animal-island-slides/scripts/extract-pptx.py" [文件路径] .
   ```
3. 展示提取结果（每张幻灯片的文字内容和图片列表），用 AskUserQuestion 确认：
   - 内容看起来正确，继续 / 有问题，先调整
4. 确认后，以提取内容为输入，从 Phase 2 开始执行（规划大纲 → 生成 HTML）
5. 提取出的图片以相对路径引用，放在与 HTML 同名的 `_assets/` 文件夹中

---

## Phase 5：导出（可选）

生成完成后，询问用户是否需要导出：

**导出为 PDF**：
```bash
bash "${CLAUDE_PLUGIN_ROOT}/skills/animal-island-slides/scripts/export-pdf.sh" ./[文件名].html
```

**部署到 Vercel（生成永久分享链接）**：
```bash
bash "${CLAUDE_PLUGIN_ROOT}/skills/animal-island-slides/scripts/deploy.sh" ./[文件名].html
```

如果 `CLAUDE_PLUGIN_ROOT` 未注入（例如用户直接 clone 仓库手动运行，而非通过 `/plugin install`），回退到仓库内相对路径 `bash skills/animal-island-slides/scripts/export-pdf.sh ...`，并提示用户参考 [README](../../README.md) 的安装说明。

---

## Phase 6：增强已有演示文稿（Mode C）

修改已有 HTML 文件时的额外规则：

1. **先读文件**，统计每张幻灯片的现有元素数量，对照密度上限
2. **添加内容前**：确认该幻灯片还有空间；若已满，自动拆分为新幻灯片
3. **添加图片**：必须有 `max-height: min(50vh,400px)`
4. **修改后逐项验证** Phase 3 的检查清单
5. **不替换整个文件**，只用 Edit 工具做精确修改

---

## 禁止事项

- 禁止在幻灯片内出现滚动条
- 禁止使用固定 px/rem 字号（必须用 clamp）
- 禁止 emoji（用内联 SVG）
- 禁止直接在元素上写 `animation:`（用 `.slide.active` 驱动）
- 禁止超出各布局密度上限而不拆分
- 禁止自行发明不在 html-template.md 中的布局或色彩系统
