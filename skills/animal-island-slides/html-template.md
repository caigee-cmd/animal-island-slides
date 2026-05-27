# Animal Island Slides — HTML Template

**何时读取**：在生成幻灯片的 Phase 3 阶段读取本文件，将 BASE 代码嵌入每份输出，然后按需组合 LAYOUT 片段填充幻灯片内容。

---

## 硬性规则（违反则输出无效）

- 每个 `.slide` 必须有 `height: 100dvh; overflow: hidden` — 绝不允许幻灯片内滚动
- 布局类（`.slide-cover` / `.slide-bullets` / `.slide-quote` / `.slide-divider` 等）**禁止设置 `position`** — `.slide` 已是 `position: absolute`，足以作为 `::before/::after` 的定位上下文；若覆盖为 `relative`，幻灯片会脱离绝对定位、沿文档流堆到 deck 视口外
- 所有字号和间距使用 `clamp(min, preferred, max)` — 绝不使用固定 px/rem
- 内容超出当前布局密度上限时，自动拆分为两张幻灯片
- 所有入场动画使用 `.slide.entered .element` 驱动 transition，禁止在元素上直接写 `animation:`（浏览器会在不可见父元素内跳过 animation）
- 禁止使用 emoji — 图标一律用内联 SVG

### 每种布局的内容密度上限

| 布局 | 最大内容 |
|---|---|
| Cover | 1 标题 + 1 副标题 + 1 按钮 |
| Card Grid | 1 标题 + 最多 6 张卡片（3×2） |
| Quote + Checklist | 1 引用段落 + 最多 4 条 check item |
| Text Bullets | 1 标题 + 最多 5 条要点 |
| Two Column | 左侧文字块 + 右侧图片或数据块 |
| Section Divider | 章节号 + 章节名 + 1 句描述 |
| Big Stat Hero | 1 eyebrow + 1 大数字 + 1 caption + 1 sub |
| Timeline | 1 标题 + 最多 5 节点（每节点说明 ≤22 字） |
| Comparison | 1 标题 + 2 panel（每 panel ≤4 条列表项） |
| Pull Quote | 1 引言段（≤3 行/40 字） + 1 作者卡 |
| Stat Strip | 1 标题 + 3-4 指标（每指标：数字 + label + 1 行 trend） |
| Process Flow | 1 标题 + 3-5 步骤（每步骤说明 ≤30 字） |
| Image Hero | 1 图（≤60vh） + 1 caption（≤2 行 desc） |
| Code Slide | 1 标题 + 1 代码卡（≤10 行）+ 1 行 caption |
| NookPhone Showcase | 1 标题 + 9 个 app（≤6 字标签） + 2 段说明 |
| FAQ | 1 标题 + 3-4 组 Q+A（Q ≤25 字, A ≤60 字） |
| Island Scene | 1 eyebrow + 1 主标题（≤16 字）+ 1 caption（≤40 字） |
| Color Wheel | 1 标题 + 4-13 chip（label ≤6 字 + sub ≤8 字） + 1 行可选注脚 |
| Dialogue Modal | 1 说话者牌（2 字头像 + 姓名 ≤6 字 + 角色 ≤10 字）+ 1-2 段对话（总 ≤100 字） |
| Icon Constellation | 1 中央标题（≤12 字）+ 可选 caption（≤24 字）+ 4-8 个图标节点（label ≤8 字） |
| Tabs Switcher | 1 标题 + 2-4 个 tab + 每个 panel 内 1 副标题 + 2-4 个要点（每要点 ≤40 字） |
| Collapse Stack | 1 标题 + 3-5 个 collapse-item（每 item：Q ≤30 字 + A 1-2 段 / ≤4 li） |
| Settings Panel | 1 标题 + 可选 caption + 3-6 行（每行：图标 + 标签 + 描述 + 控件） |

---

## 风格语法（必须遵循）

下面这些不是"可选审美建议"，而是这套模板是否真的像一份 AC 主题 deck 的分界线：

- **不要只用动森配色。** 仅有绿色、米色、圆角卡片，不足以构成 AC PPT 风格。
- **每张 slide 至少有一个场景锚点。** 从 `木牌 / 纸板 / 房屋 / 栅栏 / 邮筒 / 树 / 物品栏 / 角色插画` 中至少取一个。
- **标题优先落在具象容器里。** 封面、章节、结尾页优先用木牌；内容页优先用纸张面板，不要让大标题直接飘在空背景上。
- **内容页不是纯卡片墙。** 卡片要嵌在一块更大的"纸面 / 告示板 / 展示板"里，而不是直接浮在留白上。
- **一份 deck 内至少 3 页出现具象道具。** 封面 1 页、章节/目录 1 页、内容页或结尾 1 页，是最低要求。
- **道具要服务版式，而不是装饰角落。** 优先把道具作为视觉骨架的一部分，而不是最后随手贴贴纸。

如果一页只剩下"圆角卡片 + 动森色 + 图标"，默认视为风格不够明显，需要继续增强。

---

## BASE — 每份输出都必须包含的完整基础代码

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title><!-- 演示文稿标题 --></title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;500;600;700;800;900&family=Noto+Sans+SC:wght@400;500;700&display=swap" rel="stylesheet">
<style>
/* ── DESIGN TOKENS ─────────────────────────────────────── */
:root {
  --font-main:      'Nunito', 'Noto Sans SC', -apple-system, 'PingFang SC', sans-serif;

  --bg-parchment:   #f8f8f0;
  --bg-card:        rgb(247, 243, 223);
  --bg-green:       #7DC395;
  --bg-mint-light:  #e6f9f6;

  --text-primary:   #794f27;
  --text-body:      #725d42;
  --text-secondary: #9f927d;
  --text-muted:     #8a7b66;

  --accent-teal:    #19c8b9;
  --accent-teal-a:  #11a89b;
  --accent-yellow:  #ffcc00;
  --accent-yellow-d:#e0b800;
  --green:          #6fba2c;

  /* NookPhone 卡片配色（给 card-icon 的 background 用）*/
  --card-pink:   #f8a6b2;
  --card-purple: #b77dee;
  --card-blue:   #889df0;
  --card-yellow: #f7cd67;
  --card-orange: #e59266;
  --card-teal:   #82d5bb;
  --card-green:  #8ac68a;
  --card-red:    #fc736d;
  --card-lime:   #d1da49;

  --border-std:  2px solid #9f927d;

  --shadow-btn:  0 5px 0 0 #bdaea0;
  --shadow-btn-h:0 6px 0 0 #bdaea0;
  --shadow-btn-a:0 1px 0 0 #bdaea0;
  --shadow-card: 0 4px 10px rgba(107,92,67,0.42);

  --radius-pill:    50px;
  --radius-card:    20px;
  --radius-organic: 40px 35px 45px 38px / 38px 45px 35px 40px;
  --radius-sm:      12px;

  --ease-ac: cubic-bezier(0.4, 0, 0.2, 1);
}

/* ── RESET & SLIDE ENGINE ──────────────────────────────── */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

html, body {
  width: 100%; height: 100%;
  font-family: var(--font-main);
  background: #3d3028;
  overflow: hidden;
}

.deck { width: 100vw; height: 100vh; position: relative; overflow: hidden; }

.slide {
  position: absolute; inset: 0;
  width: 100%; height: 100dvh;
  overflow: hidden;
  display: flex; flex-direction: column;
  opacity: 0; transform: translateX(60px);
  transition: opacity 0.4s var(--ease-ac), transform 0.4s var(--ease-ac);
  pointer-events: none;
}
.slide.active  { opacity: 1; transform: translateX(0); pointer-events: all; }
.slide.exit    { opacity: 0; transform: translateX(-60px); }

/* ── NAVIGATION ────────────────────────────────────────── */
.nav-dots {
  position: fixed; bottom: clamp(16px,3vh,28px); left: 50%;
  transform: translateX(-50%); display: flex; gap: 10px; z-index: 100;
}
.dot {
  width: 10px; height: 10px; border-radius: 50%;
  background: rgba(121,79,39,0.25); border: 2px solid rgba(121,79,39,0.4);
  cursor: pointer; transition: all 0.25s var(--ease-ac);
}
.dot.active { background: var(--text-primary); transform: scale(1.3); }

.nav-arrow {
  position: fixed; top: 50%; transform: translateY(-50%); z-index: 100;
  width: 44px; height: 44px; border-radius: 50%; border: none;
  background: var(--bg-card); box-shadow: var(--shadow-btn);
  cursor: pointer; color: var(--text-body);
  display: flex; align-items: center; justify-content: center;
  transition: all 0.25s var(--ease-ac);
}
.nav-arrow:hover  { box-shadow: var(--shadow-btn-h); transform: translateY(calc(-50% - 1px)); }
.nav-arrow:active { box-shadow: var(--shadow-btn-a); transform: translateY(calc(-50% + 2px)); }
.nav-arrow.prev { left:  clamp(12px,2vw,28px); }
.nav-arrow.next { right: clamp(12px,2vw,28px); }

/* ── SHARED KEYFRAMES ──────────────────────────────────── */
@keyframes leafFloat {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50%       { transform: translateY(-10px) rotate(6deg); }
}

/* ── RESPONSIVE BREAKPOINTS ────────────────────────────── */
@media (max-height: 600px) {
  .cover-title { font-size: clamp(28px,5vw,48px) !important; }
  .card-grid   { gap: 10px; }
  .ac-card     { padding: 12px 14px; }
}
@media (max-width: 768px) {
  .card-grid    { grid-template-columns: repeat(2,1fr); }
  .two-col-layout, .quote-layout { grid-template-columns: 1fr; }
}
@media (prefers-reduced-motion: reduce) {
  .slide, .ac-card, .nav-arrow,
  .cover-badge, .cover-title, .cover-subtitle, .cover-btn,
  .slide-header, .blob-quote, .checklist,
  .bullet-list, .col-text, .col-visual { transition: none !important; }
  .leaf { animation: none; }
}

/* ── LAYOUT-SPECIFIC CSS（见下方各 LAYOUT 区块）── */
/* 在此处追加所用布局的 CSS */
</style>
</head>
<body>

<div class="deck" id="deck">
  <!-- 在此处插入 <section class="slide ..."> 幻灯片 -->
</div>

<button class="nav-arrow prev" onclick="prev()" aria-label="上一张">
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="15 18 9 12 15 6"/></svg>
</button>
<button class="nav-arrow next" onclick="next()" aria-label="下一张">
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="9 18 15 12 9 6"/></svg>
</button>
<div class="nav-dots" id="dots"></div>

<script>
const slides = document.querySelectorAll('.slide');
const dotsEl = document.getElementById('dots');
let cur = 0;

slides.forEach((_, i) => {
  const d = document.createElement('button');
  d.className = 'dot' + (i === 0 ? ' active' : '');
  d.setAttribute('aria-label', `第 ${i+1} 张`);
  d.onclick = () => goTo(i);
  dotsEl.appendChild(d);
});

// double-rAF: 先让 slide 变 active（浏览器计算子元素初始样式）
// 再加 entered 触发子元素 transition，避免在不可见父元素内 transition 无法播放
function enter(slide) {
  requestAnimationFrame(() => requestAnimationFrame(() => slide.classList.add('entered')));
}

function goTo(idx) {
  if (idx === cur) return;
  slides[cur].classList.remove('active', 'entered');
  slides[cur].classList.add('exit');
  setTimeout(() => slides[cur].classList.remove('exit'), 400);
  cur = (idx + slides.length) % slides.length;
  slides[cur].classList.add('active');
  enter(slides[cur]);
  document.querySelectorAll('.dot').forEach((d, i) => d.classList.toggle('active', i === cur));
}
function next() { goTo(cur + 1); }
function prev() { goTo(cur - 1); }

enter(slides[0]); // 初始幻灯片

document.addEventListener('keydown', e => {
  if (['ArrowRight','ArrowDown',' '].includes(e.key)) { e.preventDefault(); next(); }
  if (['ArrowLeft','ArrowUp'].includes(e.key))         { e.preventDefault(); prev(); }
});
let sx = 0;
document.addEventListener('touchstart', e => { sx = e.touches[0].clientX; });
document.addEventListener('touchend',   e => { const dx = e.changedTouches[0].clientX - sx; if (Math.abs(dx) > 40) dx < 0 ? next() : prev(); });
</script>
</body>
</html>
```

---

## Scenic Utilities（跨布局复用）

以下 3 组组件建议视为基础语法，而不是某个 layout 的私货。封面、章节页、目录页、结束页优先复用它们：

### 1. Wooden Plank（木牌标题容器）

- 适合：Cover / Section Divider / 结尾页 / 目录页标题
- 作用：把标题从"网页文字"变成"岛上告示牌"
- 做法：标题、副标题放入 `.ac-plank`，而不是直接裸排

### 2. Scenic Props（场景道具）

- 适合：封面角落、章节页底部、内容页侧边
- 推荐优先级：`房屋 / 栅栏 / 邮筒 / 路灯 / 树`
- 原则：道具要围绕内容形成构图，不要随机分散

### 3. Inventory Strip（底部物品栏）

- 适合：封面、目录、结束页
- 作用：补足"游戏 UI"层，不让页面只剩插画背景
- 注意：只在 1-3 个关键页使用，避免页页重复

如果需要强风格封面，优先顺序是：

1. `ac-plank` 承载标题
2. 两侧 `ac-prop` 建立场景
3. 底部 `ac-inv-strip` 收尾

---

## LAYOUT A — Cover（封面 / Island Welcome）

**使用场景**：演示文稿第一张，或各章节的大标题页。  
**背景**：岛屿绿 `--bg-green`，底部椭圆波浪露出羊皮纸色。
**强风格要求**：标题优先放进木牌，至少搭配 2 个场景道具；不要只做"绿底 + 大字 + 按钮"。

### CSS（追加到 `<style>` 末尾）

```css
.slide-cover {
  background: var(--bg-green);
  align-items: center; justify-content: center; text-align: center;
}
.slide-cover::after {
  content: '';
  position: absolute; bottom: 0; left: 0; right: 0;
  height: clamp(60px,12vh,100px);
  background: var(--bg-parchment);
  clip-path: ellipse(55% 100% at 50% 100%);
}

/* 浮动叶片 */
.leaf { position: absolute; width: clamp(60px,8vw,120px); opacity: 0.3; animation: leafFloat 4s ease-in-out infinite; }
.leaf-1 { top: 8%;  left: 5%;  animation-delay: 0s; }
.leaf-2 { top: 15%; right: 8%; animation-delay: 1.5s; transform: scaleX(-1); }
.leaf-3 { bottom: 18%; left: 10%; animation-delay: 0.8s; }
.leaf-4 { bottom: 20%; right: 6%; animation-delay: 2.2s; transform: scaleX(-1); }

.cover-badge {
  display: inline-flex; align-items: center; gap: 8px;
  padding: 8px 20px;
  background: var(--bg-card); border-radius: var(--radius-pill);
  box-shadow: var(--shadow-btn);
  font-size: clamp(11px,1.5vw,13px); font-weight: 700;
  color: var(--text-muted); letter-spacing: 0.08em; text-transform: uppercase;
  margin-bottom: clamp(16px,3vh,28px);
}
.cover-title {
  font-size: clamp(36px,6vw,80px); font-weight: 900; color: #fff;
  line-height: 1.1; letter-spacing: -0.01em;
  text-shadow: 0 4px 0 rgba(0,0,0,0.12);
  margin-bottom: clamp(12px,2vh,20px);
}
.cover-title em { font-style: normal; color: var(--accent-yellow); }
.cover-subtitle {
  font-size: clamp(14px,2vw,20px); font-weight: 600;
  color: rgba(255,255,255,0.85); letter-spacing: 0.02em;
  margin-bottom: clamp(24px,4vh,40px);
}
.cover-btn {
  display: inline-flex; align-items: center; gap: 10px;
  padding: 0 clamp(24px,3vw,36px); height: clamp(44px,6vh,56px);
  background: var(--accent-yellow); color: var(--text-body);
  border: none; border-radius: var(--radius-pill);
  box-shadow: 0 5px 0 0 var(--accent-yellow-d);
  font-family: var(--font-main); font-size: clamp(14px,1.8vw,17px);
  font-weight: 700; letter-spacing: 0.03em; cursor: pointer;
  transition: all 0.25s var(--ease-ac);
}
.cover-btn:hover  { box-shadow: 0 6px 0 0 var(--accent-yellow-d); transform: translateY(-1px); }
.cover-btn:active { box-shadow: 0 1px 0 0 var(--accent-yellow-d); transform: translateY(2px); }

/* 入场动画 — 由 .slide.entered 驱动（JS double-rAF 触发，见 BASE JS 部分）*/
.cover-badge, .cover-title, .cover-subtitle, .cover-btn {
  opacity: 0; transform: translateY(16px);
  transition: opacity 0.5s var(--ease-ac), transform 0.5s var(--ease-ac);
}
.slide.entered .cover-badge    { opacity:1; transform:translateY(0); transition-delay:0.05s; }
.slide.entered .cover-title    { opacity:1; transform:translateY(0); transition-delay:0.15s; }
.slide.entered .cover-subtitle { opacity:1; transform:translateY(0); transition-delay:0.25s; }
.slide.entered .cover-btn      { opacity:1; transform:translateY(0); transition-delay:0.35s; }
.slide:not(.entered) .cover-badge,
.slide:not(.entered) .cover-title,
.slide:not(.entered) .cover-subtitle,
.slide:not(.entered) .cover-btn { transition-delay: 0s; }
```

### HTML 片段

```html
<section class="slide slide-cover" data-slide="N">
  <!-- 叶片装饰（可按需减少） -->
  <svg class="leaf leaf-1" viewBox="0 0 120 80" fill="none"><path d="M10 70 Q30 10 110 5 Q80 60 10 70Z" fill="#fff"/><path d="M10 70 Q60 40 110 5" stroke="#fff" stroke-width="2" opacity="0.6"/></svg>
  <svg class="leaf leaf-2" viewBox="0 0 120 80" fill="none"><path d="M10 70 Q30 10 110 5 Q80 60 10 70Z" fill="#fff"/></svg>
  <svg class="leaf leaf-3" viewBox="0 0 100 70" fill="none"><path d="M8 60 Q20 8 95 5 Q65 55 8 60Z" fill="#fff"/></svg>
  <svg class="leaf leaf-4" viewBox="0 0 100 70" fill="none"><path d="M8 60 Q20 8 95 5 Q65 55 8 60Z" fill="#fff"/></svg>

  <!-- 推荐：至少放两枚场景道具 -->
  <!-- 左下：邮筒 / 右下：小屋 / 栅栏 / 路灯，按内容选 -->

  <div style="position:relative;z-index:1;display:flex;flex-direction:column;align-items:center;gap:clamp(16px,3vh,24px);">
    <div class="cover-badge"><!-- 副标签文字，如"第一章" / 项目名 --></div>

    <!-- 推荐：标题放进木牌，而不是裸标题 -->
    <div class="ac-plank">
      <svg class="ac-plank-leaf" viewBox="0 0 60 60" fill="none"><!-- leaf --></svg>
      <h1 class="cover-title"><!-- 主标题，<em> 包裹高亮词 --></h1>
      <p class="cover-subtitle"><!-- 副标题或 tagline --></p>
    </div>

    <button class="cover-btn" onclick="next()">
      <!-- 按钮文字，如"开始" / "下一节" -->
      <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="9 18 15 12 9 6"/></svg>
    </button>
  </div>

  <!-- 可选：封面/目录/结束页追加 inventory strip -->
  <!-- <div class="ac-inv-strip floating">...</div> -->
</section>
```

---

## LAYOUT B — Card Grid（功能卡片 / NookPhone Grid）

**使用场景**：功能列表、对比项目、团队成员、产品特性。最多 6 张卡片。  
**背景**：羊皮纸 `--bg-parchment`。
**强风格要求**：卡片不要直接浮在空背景上，优先整体放进一张大纸面 / 展示板里；建议至少一侧搭配 `房屋 / 栅栏 / 邮筒` 之一做场景锚点。

### CSS

```css
.slide-grid {
  background: var(--bg-parchment);
  padding: clamp(32px,5vh,60px) clamp(32px,6vw,80px) clamp(48px,8vh,80px);
  justify-content: center;
}

.slide-header {
  margin-bottom: clamp(20px,3.5vh,36px);
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .slide-header      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .slide-header { transition-delay:0s; }

.slide-label {
  font-size: clamp(10px,1.2vw,12px); font-weight: 700;
  color: var(--accent-teal); letter-spacing: 0.12em; text-transform: uppercase;
  margin-bottom: 6px;
}
.slide-title {
  font-size: clamp(24px,3.5vw,48px); font-weight: 900;
  color: var(--text-primary); line-height: 1.15;
}

.card-grid {
  display: grid;
  grid-template-columns: repeat(3,1fr);  /* 2 列改为 repeat(2,1fr) */
  gap: clamp(12px,1.8vw,20px);
}

.ac-card {
  background: var(--bg-card); border-radius: var(--radius-card);
  padding: clamp(16px,2.5vw,24px); box-shadow: var(--shadow-card);
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.4s var(--ease-ac), transform 0.4s var(--ease-ac), box-shadow 0.25s var(--ease-ac);
}
.ac-card:hover { transform: translateY(-4px); box-shadow: 0 12px 28px rgba(107,92,67,0.18); }

.slide.entered .ac-card            { opacity:1; transform:translateY(0); }
.slide.entered .ac-card:nth-child(1) { transition-delay:0.10s; }
.slide.entered .ac-card:nth-child(2) { transition-delay:0.17s; }
.slide.entered .ac-card:nth-child(3) { transition-delay:0.24s; }
.slide.entered .ac-card:nth-child(4) { transition-delay:0.31s; }
.slide.entered .ac-card:nth-child(5) { transition-delay:0.38s; }
.slide.entered .ac-card:nth-child(6) { transition-delay:0.45s; }
.slide:not(.entered) .ac-card { transition-delay:0s; }

.card-icon {
  width: clamp(36px,4vw,52px); height: clamp(36px,4vw,52px);
  border-radius: var(--radius-sm);
  display: flex; align-items: center; justify-content: center;
  margin-bottom: clamp(10px,1.5vh,16px);
  box-shadow: 0 3px 0 0 rgba(0,0,0,0.1);
}
.card-name {
  font-size: clamp(13px,1.5vw,16px); font-weight: 700;
  color: var(--text-body); margin-bottom: 4px;
}
.card-desc {
  font-size: clamp(11px,1.2vw,13px); font-weight: 500;
  color: var(--text-secondary); line-height: 1.5;
}
```

### HTML 片段

```html
<section class="slide slide-grid" data-slide="N">
  <div class="slide-header">
    <div class="slide-label"><!-- 分类标签，如"核心特性" --></div>
    <h2 class="slide-title"><!-- 幻灯片标题 --></h2>
  </div>

  <div class="card-grid">
    <!-- 每张卡片。card-icon background 从 NookPhone 配色中选一个 -->
    <div class="ac-card">
      <div class="card-icon" style="background: var(--card-teal);">
        <!-- 内联 SVG 图标，stroke="#fff"，width/height="22" -->
      </div>
      <div class="card-name"><!-- 卡片标题 --></div>
      <div class="card-desc"><!-- 1-2 句说明，不超过 40 字 --></div>
    </div>
    <!-- 重复至多 6 个 .ac-card -->
  </div>
</section>
```

---

## LAYOUT C — Quote + Checklist（引用 + 要点 / Cozy Indoors）

**使用场景**：展示核心观点 + 支撑要点；数据结论 + 验证列表；用户评价 + 功能清单。  
**背景**：暖米色 `--bg-card`，点阵纹理。最多 4 条 check item。

### CSS

```css
.slide-quote {
  background: var(--bg-card);
  padding: clamp(32px,5vh,60px) clamp(40px,8vw,120px);
  justify-content: center;
}
.slide-quote::before {
  content: ''; position: absolute; inset: 0;
  background-image: radial-gradient(circle, rgba(159,146,125,0.12) 1.5px, transparent 1.5px);
  background-size: 28px 28px; pointer-events: none;
}

.quote-layout {
  display: grid; grid-template-columns: 1fr 1fr;
  gap: clamp(32px,5vw,64px); align-items: center;
  position: relative; z-index: 1; width: 100%;
}

/* 左侧：有机 blob 引用框 */
.blob-quote {
  background: var(--bg-parchment); border-radius: var(--radius-organic);
  padding: clamp(24px,3.5vw,40px); box-shadow: var(--shadow-card);
  position: relative;
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.45s var(--ease-ac) 0.1s, transform 0.45s var(--ease-ac) 0.1s;
}
.slide.entered .blob-quote      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .blob-quote { transition-delay:0s; }

.blob-quote::before {
  content: '"'; position: absolute; top: -8px; left: clamp(16px,2.5vw,28px);
  font-size: clamp(48px,6vw,72px); font-weight: 900;
  color: var(--accent-teal); line-height: 1; font-family: Georgia, serif;
}
.quote-text {
  font-size: clamp(15px,2vw,22px); font-weight: 600; color: var(--text-body);
  line-height: 1.6; letter-spacing: 0.01em;
  padding-top: clamp(20px,2.5vh,32px);
}
.quote-text strong { color: var(--accent-teal); }

.quote-author { margin-top: clamp(12px,1.8vh,20px); display: flex; align-items: center; gap: 10px; }
.author-avatar {
  width: clamp(30px,3.5vw,42px); height: clamp(30px,3.5vw,42px);
  border-radius: 50%; background: var(--accent-teal);
  display: flex; align-items: center; justify-content: center;
  font-size: clamp(11px,1.4vw,14px); font-weight: 800; color: #fff; letter-spacing: 0.02em;
  box-shadow: 0 3px 0 0 var(--accent-teal-a);
}
.author-name {
  font-size: clamp(12px,1.4vw,15px); font-weight: 700;
  color: var(--text-muted); letter-spacing: 0.02em;
}

/* 右侧：checklist */
.checklist {
  display: flex; flex-direction: column; gap: clamp(10px,1.8vh,16px);
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.45s var(--ease-ac) 0.2s, transform 0.45s var(--ease-ac) 0.2s;
}
.slide.entered .checklist      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .checklist { transition-delay:0s; }

.check-item {
  display: flex; align-items: flex-start;
  gap: clamp(10px,1.5vw,16px);
  padding: clamp(12px,1.8vh,18px) clamp(14px,2vw,20px);
  background: var(--bg-parchment); border-radius: var(--radius-sm);
  border: var(--border-std); transition: all 0.25s var(--ease-ac);
}
.check-item:hover { transform: translateX(4px); border-color: var(--accent-teal); }
.check-icon {
  width: clamp(22px,2.5vw,30px); height: clamp(22px,2.5vw,30px);
  border-radius: 8px; background: var(--accent-teal);
  display: flex; align-items: center; justify-content: center; flex-shrink: 0;
  box-shadow: 0 3px 0 0 var(--accent-teal-a);
}
.check-text { font-size: clamp(13px,1.5vw,16px); font-weight: 600; color: var(--text-body); line-height: 1.4; }
.check-sub  { font-size: clamp(11px,1.2vw,13px); font-weight: 500; color: var(--text-secondary); margin-top: 2px; display: block; }
```

### HTML 片段

```html
<section class="slide slide-quote" data-slide="N">
  <div class="quote-layout">
    <div class="blob-quote">
      <p class="quote-text">
        <!-- 核心观点，1-3 句。<strong> 包裹关键词 -->
      </p>
      <div class="quote-author">
        <div class="author-avatar"><!-- 2 字缩写 --></div>
        <div class="author-name"><!-- 姓名 · 职位 --></div>
      </div>
    </div>

    <div class="checklist">
      <!-- 最多 4 条，超出则新建一张幻灯片 -->
      <div class="check-item">
        <div class="check-icon">
          <svg width="13" height="13" viewBox="0 0 13 13" fill="none" stroke="#fff" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><polyline points="2 7 5 10 11 3"/></svg>
        </div>
        <div class="check-text">
          <!-- 要点标题 -->
          <span class="check-sub"><!-- 补充说明，可省略 --></span>
        </div>
      </div>
    </div>
  </div>
</section>
```

---

## LAYOUT D — Text Bullets（文字要点）

**使用场景**：纯文字内容、背景介绍、步骤说明。最多 5 条要点。  
**背景**：羊皮纸 `--bg-parchment`，左侧 teal 竖条装饰。
**强风格要求**：列表本体应放在纸张面板里，不要只做一列裸条目；右下角或侧边建议加 1 个小屋/围栏类道具，避免页面退回文档感。

### CSS

```css
.slide-bullets {
  background: var(--bg-parchment);
  padding: clamp(40px,6vh,72px) clamp(48px,10vw,160px);
  justify-content: center;
}
/* 左侧竖条 */
.slide-bullets::before {
  content: ''; position: absolute;
  left: clamp(24px,4vw,56px); top: 20%; bottom: 20%;
  width: 4px; border-radius: 2px;
  background: linear-gradient(180deg, var(--accent-teal) 0%, var(--accent-yellow) 100%);
}

.bullets-header {
  margin-bottom: clamp(24px,4vh,40px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .bullets-header      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .bullets-header { transition-delay:0s; }

.bullet-list {
  display: flex; flex-direction: column; gap: clamp(12px,2vh,20px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.15s, transform 0.4s var(--ease-ac) 0.15s;
}
.slide.entered .bullet-list      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .bullet-list { transition-delay:0s; }

.bullet-item {
  display: flex; align-items: baseline; gap: clamp(12px,1.8vw,20px);
  padding: clamp(14px,2vh,20px) clamp(16px,2.5vw,24px);
  background: var(--bg-card); border-radius: var(--radius-card);
  border-left: 4px solid var(--accent-teal);
  transition: transform 0.2s var(--ease-ac), border-color 0.2s;
}
.bullet-item:hover { transform: translateX(6px); border-color: var(--accent-yellow); }

.bullet-num {
  font-size: clamp(18px,2.5vw,28px); font-weight: 900;
  color: var(--accent-teal); min-width: 1.5ch; line-height: 1;
  flex-shrink: 0;
}
.bullet-body { flex: 1; }
.bullet-title {
  font-size: clamp(14px,1.8vw,20px); font-weight: 700;
  color: var(--text-primary); margin-bottom: 4px; line-height: 1.3;
}
.bullet-desc {
  font-size: clamp(11px,1.3vw,14px); font-weight: 500;
  color: var(--text-secondary); line-height: 1.5;
}
```

### HTML 片段

```html
<section class="slide slide-bullets" data-slide="N">
  <div class="bullets-header">
    <div class="slide-label"><!-- 分类标签 --></div>
    <h2 class="slide-title"><!-- 幻灯片标题 --></h2>
  </div>
  <div class="bullet-list">
    <!-- 最多 5 条，超出则拆分 -->
    <div class="bullet-item">
      <div class="bullet-num">01</div>
      <div class="bullet-body">
        <div class="bullet-title"><!-- 要点标题 --></div>
        <div class="bullet-desc"><!-- 说明，可省略 --></div>
      </div>
    </div>
  </div>
</section>
```

---

## LAYOUT E — Two Column（双栏图文）

**使用场景**：左文右图、左数据右说明、Before/After 对比。  
**背景**：可用 `--bg-parchment` 或 `--bg-card`。
**强风格要求**：左右两栏优先被同一张"纸板"包住；右侧视觉区不要只是抽象数据框，优先做成贴纸感示意卡、便签页或带道具的小场景。

### CSS

```css
.slide-twocol {
  background: var(--bg-parchment);
  padding: clamp(32px,5vh,64px) clamp(32px,6vw,80px) clamp(48px,8vh,80px);
  justify-content: center;
}

.two-col-layout {
  display: grid; grid-template-columns: 1fr 1fr;
  gap: clamp(24px,4vw,56px); align-items: center;
  width: 100%;
}

.col-text {
  opacity: 0; transform: translateX(-16px);
  transition: opacity 0.45s var(--ease-ac) 0.1s, transform 0.45s var(--ease-ac) 0.1s;
}
.slide.entered .col-text      { opacity:1; transform:translateX(0); }
.slide:not(.entered) .col-text { transition-delay:0s; }

.col-text .slide-label { margin-bottom: 8px; }
.col-text .slide-title { margin-bottom: clamp(16px,2.5vh,28px); }

.col-body {
  font-size: clamp(13px,1.6vw,17px); font-weight: 500;
  color: var(--text-body); line-height: 1.7;
}
.col-body p + p { margin-top: 1em; }
.col-body strong { color: var(--text-primary); font-weight: 700; }

.col-visual {
  opacity: 0; transform: translateX(16px);
  transition: opacity 0.45s var(--ease-ac) 0.2s, transform 0.45s var(--ease-ac) 0.2s;
  display: flex; align-items: center; justify-content: center;
}
.slide.entered .col-visual      { opacity:1; transform:translateX(0); }
.slide:not(.entered) .col-visual { transition-delay:0s; }

/* 图片容器 */
.col-visual img {
  max-width: 100%; max-height: min(50vh,400px);
  border-radius: var(--radius-card); box-shadow: var(--shadow-card);
  object-fit: cover;
}

/* 无图时的占位数据卡 */
.data-card {
  width: 100%; background: var(--bg-card);
  border-radius: var(--radius-card); padding: clamp(24px,3vw,36px);
  box-shadow: var(--shadow-card); text-align: center;
}
.data-number {
  font-size: clamp(48px,7vw,96px); font-weight: 900;
  color: var(--accent-teal); line-height: 1;
  text-shadow: 0 4px 0 rgba(25,200,185,0.2);
}
.data-unit  { font-size: clamp(18px,2.5vw,28px); font-weight: 700; color: var(--text-secondary); }
.data-label { font-size: clamp(12px,1.4vw,15px); font-weight: 600; color: var(--text-muted); margin-top: 8px; }
```

### HTML 片段

```html
<section class="slide slide-twocol" data-slide="N">
  <div class="two-col-layout">
    <div class="col-text">
      <div class="slide-label"><!-- 标签 --></div>
      <h2 class="slide-title"><!-- 标题 --></h2>
      <div class="col-body">
        <p><!-- 正文段落，每段不超过 60 字 --></p>
      </div>
    </div>

    <div class="col-visual">
      <!-- 选择一种：图片 / 数据卡 / 自定义 SVG 插图 -->

      <!-- 图片 -->
      <img src="路径" alt="描述">

      <!-- 或：数据卡 -->
      <div class="data-card">
        <div class="data-number">98<span class="data-unit">%</span></div>
        <div class="data-label"><!-- 数据说明 --></div>
      </div>
    </div>
  </div>
</section>
```

---

## LAYOUT F — Section Divider（章节分隔）

**使用场景**：多章节演示中的章节过渡页，比 Cover 更轻量。  
**背景**：薄荷绿渐变，中央章节号 + 标题。
**强风格要求**：优先做成"吊挂木牌"或"告示牌"，而不是纯大字号章节页。

### CSS

```css
.slide-divider {
  background: linear-gradient(135deg, var(--bg-green) 0%, #5aad82 100%);
  align-items: center; justify-content: center; text-align: center;
}
/* 右下角大号装饰数字 */
.slide-divider::after {
  content: attr(data-chapter);
  position: absolute; right: -0.05em; bottom: -0.2em;
  font-size: clamp(160px,25vw,300px); font-weight: 900; line-height: 1;
  color: rgba(255,255,255,0.07); pointer-events: none; user-select: none;
}

.divider-inner {
  position: relative; z-index: 1;
  opacity: 0; transform: translateY(20px);
  transition: opacity 0.5s var(--ease-ac) 0.1s, transform 0.5s var(--ease-ac) 0.1s;
}
.slide.entered .divider-inner      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .divider-inner { transition-delay:0s; }

.divider-num {
  font-size: clamp(12px,1.5vw,14px); font-weight: 800;
  color: rgba(255,255,255,0.7); letter-spacing: 0.2em; text-transform: uppercase;
  margin-bottom: clamp(12px,2vh,20px);
}
.divider-title {
  font-size: clamp(32px,5.5vw,72px); font-weight: 900; color: #fff;
  line-height: 1.1; text-shadow: 0 4px 0 rgba(0,0,0,0.1);
  margin-bottom: clamp(12px,2vh,20px);
}
.divider-desc {
  font-size: clamp(13px,1.6vw,18px); font-weight: 600;
  color: rgba(255,255,255,0.8);
}
```

### HTML 片段

```html
<!-- data-chapter 的值会渲染为右下角大号装饰数字 -->
<section class="slide slide-divider" data-slide="N" data-chapter="02">
  <div class="divider-inner">
    <!-- 推荐：把章节信息包进木牌或吊牌里 -->
    <div class="ac-plank hanger">
      <svg class="ac-plank-leaf" viewBox="0 0 60 60" fill="none"><!-- leaf --></svg>
      <div class="divider-num">Chapter 02</div>
      <h2 class="divider-title"><!-- 章节标题 --></h2>
      <p class="divider-desc"><!-- 一句话简介，可省略 --></p>
    </div>
  </div>
</section>
```

---

## LAYOUT G — Big Stat Hero（单数字英雄页）

**使用场景**：用一个超大数字作为视觉锤——季度增长、用户规模里程碑、ROI 验证。比 Two Column 的右侧数据卡更冲击。
**背景**：羊皮纸 `--bg-parchment`，可选叶片装饰。

### CSS

```css
.slide-stat {
  background: var(--bg-parchment);
  align-items: center; justify-content: center; text-align: center;
  padding: clamp(32px,5vh,64px);
}

.stat-eyebrow {
  font-size: clamp(11px,1.5vw,14px); font-weight: 700;
  color: var(--accent-teal); letter-spacing: 0.12em; text-transform: uppercase;
  margin-bottom: clamp(12px,2vh,20px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .stat-eyebrow      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .stat-eyebrow { transition-delay:0s; }

.stat-hero {
  font-size: clamp(80px,14vw,200px); font-weight: 900;
  color: var(--accent-teal); line-height: 0.95; letter-spacing: -0.02em;
  text-shadow: 0 6px 0 rgba(25,200,185,0.18);
  opacity: 0; transform: scale(0.85);
  transition: opacity 0.6s var(--ease-ac) 0.15s, transform 0.6s var(--ease-ac) 0.15s;
}
.slide.entered .stat-hero      { opacity:1; transform:scale(1); }
.slide:not(.entered) .stat-hero { transition-delay:0s; }
.stat-hero .stat-prefix { font-size: 0.55em; vertical-align: top; line-height: 1; }
.stat-hero .stat-unit   { font-size: clamp(32px,5vw,72px); font-weight: 800; color: var(--text-secondary); margin-left: 0.05em; }

.stat-caption {
  font-size: clamp(16px,2.2vw,26px); font-weight: 700;
  color: var(--text-primary); line-height: 1.3;
  margin-top: clamp(16px,2.5vh,28px);
  max-width: min(80vw, 900px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.45s var(--ease-ac) 0.3s, transform 0.45s var(--ease-ac) 0.3s;
}
.slide.entered .stat-caption      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .stat-caption { transition-delay:0s; }

.stat-sub {
  font-size: clamp(12px,1.4vw,16px); font-weight: 500;
  color: var(--text-secondary); line-height: 1.5;
  margin-top: clamp(8px,1.2vh,14px);
  max-width: min(70vw, 720px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.45s var(--ease-ac) 0.4s, transform 0.45s var(--ease-ac) 0.4s;
}
.slide.entered .stat-sub      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .stat-sub { transition-delay:0s; }
```

### HTML 片段

```html
<section class="slide slide-stat" data-slide="N">
  <!-- 可选叶片装饰，复用 LAYOUT A 的 .leaf -->
  <div class="stat-eyebrow"><!-- 顶部小标签，如"核心指标" --></div>
  <div class="stat-hero">
    <span class="stat-prefix">+</span>34<span class="stat-unit">%</span>
  </div>
  <div class="stat-caption"><!-- 1 句话核心结论 --></div>
  <div class="stat-sub"><!-- 1 句话支撑说明，可省略 --></div>
</section>
```

**密度上限**：1 eyebrow + 1 数字 + 1 caption + 1 sub line。超出请改用 Two Column。

---

## LAYOUT H — Timeline（时间线 / 路线图）

**使用场景**：横向 3-5 个节点，适合"产品演进史""Q1-Q4 路线图""项目里程碑"。
**背景**：羊皮纸 `--bg-parchment`，节点间手绘风虚线连接。

### CSS

```css
.slide-timeline {
  background: var(--bg-parchment);
  padding: clamp(40px,6vh,72px) clamp(32px,5vw,80px);
  justify-content: center;
}

.timeline-header {
  margin-bottom: clamp(32px,5vh,56px); text-align: center;
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .timeline-header      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .timeline-header { transition-delay:0s; }

.timeline-track {
  position: relative;
  display: grid; grid-auto-flow: column; grid-auto-columns: 1fr;
  gap: clamp(12px,2vw,24px);
  align-items: start;
}
/* 节点间的虚线连接 */
.timeline-track::before {
  content: ''; position: absolute;
  top: clamp(28px,3.5vw,40px);
  left: 8%; right: 8%; height: 0;
  border-top: 3px dashed var(--text-secondary); opacity: 0.45;
  z-index: 0;
}

.timeline-node {
  position: relative; z-index: 1;
  display: flex; flex-direction: column; align-items: center; text-align: center;
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .timeline-node              { opacity:1; transform:translateY(0); }
.slide.entered .timeline-node:nth-child(1) { transition-delay:0.18s; }
.slide.entered .timeline-node:nth-child(2) { transition-delay:0.28s; }
.slide.entered .timeline-node:nth-child(3) { transition-delay:0.38s; }
.slide.entered .timeline-node:nth-child(4) { transition-delay:0.48s; }
.slide.entered .timeline-node:nth-child(5) { transition-delay:0.58s; }
.slide:not(.entered) .timeline-node { transition-delay:0s; }

.timeline-dot {
  width: clamp(56px,7vw,80px); height: clamp(56px,7vw,80px);
  border-radius: 50%;
  background: var(--bg-card);
  border: 3px solid var(--accent-teal);
  box-shadow: 0 4px 0 0 var(--accent-teal-a);
  display: flex; align-items: center; justify-content: center;
  margin-bottom: clamp(12px,1.8vh,18px);
}
/* 交替配色：偶数节点用黄色 */
.timeline-node:nth-child(even) .timeline-dot {
  border-color: var(--accent-yellow);
  box-shadow: 0 4px 0 0 var(--accent-yellow-d);
}

.timeline-when {
  font-size: clamp(12px,1.4vw,15px); font-weight: 800;
  color: var(--accent-teal); letter-spacing: 0.08em; text-transform: uppercase;
  margin-bottom: 4px;
}
.timeline-node:nth-child(even) .timeline-when { color: var(--accent-yellow-d); }

.timeline-title {
  font-size: clamp(14px,1.7vw,19px); font-weight: 800;
  color: var(--text-primary); line-height: 1.25; margin-bottom: 6px;
}
.timeline-desc {
  font-size: clamp(11px,1.2vw,13px); font-weight: 500;
  color: var(--text-secondary); line-height: 1.5;
  max-width: 22ch;
}
```

### HTML 片段

```html
<section class="slide slide-timeline" data-slide="N">
  <div class="timeline-header">
    <div class="slide-label"><!-- 顶部小标签 --></div>
    <h2 class="slide-title"><!-- 标题 --></h2>
  </div>

  <div class="timeline-track">
    <!-- 每个节点；最多 5 个，超出请拆分两张 -->
    <div class="timeline-node">
      <div class="timeline-dot">
        <svg width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="var(--accent-teal)" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round">
          <!-- 节点图标 -->
        </svg>
      </div>
      <div class="timeline-when"><!-- 时间标签，如 Q1 / 2024 --></div>
      <div class="timeline-title"><!-- 节点标题 --></div>
      <div class="timeline-desc"><!-- 1 句话说明 --></div>
    </div>
    <!-- 重复至多 5 个 .timeline-node -->
  </div>
</section>
```

**密度上限**：1 标题 + 最多 5 个节点；每个节点说明 ≤22 字。

---

## LAYOUT I — Comparison（左右对比 / Before-After）

**使用场景**：方案 A vs B、改造前后、新老对比。中间 VS 徽章分隔，左卡米色、右卡岛屿绿。
**背景**：羊皮纸 `--bg-parchment`。

### CSS

```css
.slide-compare {
  background: var(--bg-parchment);
  padding: clamp(32px,5vh,60px) clamp(32px,5vw,80px);
  justify-content: center;
}

.compare-header {
  text-align: center; margin-bottom: clamp(20px,3vh,32px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .compare-header      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .compare-header { transition-delay:0s; }

.compare-grid {
  display: grid; grid-template-columns: 1fr auto 1fr;
  gap: clamp(16px,2.5vw,32px); align-items: stretch;
}

.compare-panel {
  border-radius: var(--radius-card); padding: clamp(20px,2.8vw,32px);
  box-shadow: var(--shadow-card);
  display: flex; flex-direction: column; gap: clamp(8px,1.4vh,14px);
  opacity: 0;
  transition: opacity 0.5s var(--ease-ac), transform 0.5s var(--ease-ac);
}
.compare-panel.left  { background: var(--bg-card);  transform: translateX(-20px); }
.compare-panel.right { background: var(--bg-green); transform: translateX(20px); }
.slide.entered .compare-panel       { opacity:1; transform:translateX(0); }
.slide.entered .compare-panel.left  { transition-delay:0.18s; }
.slide.entered .compare-panel.right { transition-delay:0.28s; }
.slide:not(.entered) .compare-panel { transition-delay:0s; }

.compare-tag {
  align-self: flex-start;
  padding: 4px 14px; border-radius: var(--radius-pill);
  font-size: clamp(10px,1.2vw,13px); font-weight: 800;
  letter-spacing: 0.1em; text-transform: uppercase;
}
.compare-panel.left  .compare-tag { background: var(--text-secondary); color: #fff; }
.compare-panel.right .compare-tag { background: var(--accent-yellow);  color: var(--text-body); }

.compare-heading {
  font-size: clamp(18px,2.3vw,26px); font-weight: 900;
  line-height: 1.25; margin-bottom: 4px;
}
.compare-panel.left  .compare-heading { color: var(--text-primary); }
.compare-panel.right .compare-heading { color: #fff; }

.compare-list { display: flex; flex-direction: column; gap: clamp(6px,1vh,10px); list-style: none; }
.compare-list li {
  display: flex; gap: 8px; align-items: flex-start;
  font-size: clamp(12px,1.4vw,15px); font-weight: 500; line-height: 1.5;
}
.compare-panel.left  .compare-list li { color: var(--text-body); }
.compare-panel.right .compare-list li { color: rgba(255,255,255,0.92); }
.compare-list li::before {
  content: ''; flex-shrink: 0;
  width: 8px; height: 8px; border-radius: 50%;
  margin-top: 0.55em; background: currentColor; opacity: 0.5;
}

.compare-vs {
  align-self: center;
  width: clamp(40px,4.5vw,56px); height: clamp(40px,4.5vw,56px);
  border-radius: 50%; background: var(--bg-card);
  border: var(--border-std); box-shadow: var(--shadow-btn);
  display: flex; align-items: center; justify-content: center;
  font-size: clamp(13px,1.5vw,17px); font-weight: 900;
  color: var(--text-primary); letter-spacing: 0.05em;
}
```

### HTML 片段

```html
<section class="slide slide-compare" data-slide="N">
  <div class="compare-header">
    <div class="slide-label"><!-- 顶部小标签 --></div>
    <h2 class="slide-title"><!-- 对比主题 --></h2>
  </div>

  <div class="compare-grid">
    <div class="compare-panel left">
      <div class="compare-tag">Before</div>
      <div class="compare-heading"><!-- 原方案名称 --></div>
      <ul class="compare-list">
        <li><!-- 要点 1 --></li>
        <!-- 最多 4 条 -->
      </ul>
    </div>

    <div class="compare-vs">VS</div>

    <div class="compare-panel right">
      <div class="compare-tag">After</div>
      <div class="compare-heading"><!-- 新方案名称 --></div>
      <ul class="compare-list">
        <li><!-- 要点 1 --></li>
        <!-- 最多 4 条 -->
      </ul>
    </div>
  </div>
</section>
```

**密度上限**：1 标题 + 2 panel（每 panel：1 tag + 1 heading + ≤4 list item）。

---

## LAYOUT J — Pull Quote（金句大版）

**使用场景**：单独突出一句话——客户原话、行业观点、产品哲学。比 LAYOUT C（Quote+Checklist）更聚焦，无支撑要点。
**背景**：暖米色 `--bg-card`，点阵纹理。

### CSS

```css
.slide-pullquote {
  background: var(--bg-card);
  padding: clamp(32px,5vh,72px) clamp(48px,8vw,120px);
  align-items: center; justify-content: center; text-align: center;
}
.slide-pullquote::before {
  content: ''; position: absolute; inset: 0;
  background-image: radial-gradient(circle, rgba(159,146,125,0.12) 1.5px, transparent 1.5px);
  background-size: 28px 28px; pointer-events: none;
}
.slide-pullquote > * { position: relative; z-index: 1; }

.pullquote-mark {
  font-family: Georgia, serif;
  font-size: clamp(72px,12vw,160px); font-weight: 900;
  color: var(--accent-teal); line-height: 0.6;
  margin-bottom: clamp(8px,1.5vh,16px);
  opacity: 0; transform: scale(0.7);
  transition: opacity 0.5s var(--ease-ac) 0.1s, transform 0.5s var(--ease-ac) 0.1s;
}
.slide.entered .pullquote-mark      { opacity:0.85; transform:scale(1); }
.slide:not(.entered) .pullquote-mark { transition-delay:0s; }

.pullquote-text {
  font-size: clamp(22px,3.2vw,44px); font-weight: 700;
  color: var(--text-primary); line-height: 1.3; letter-spacing: -0.005em;
  max-width: min(85vw, 1100px);
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.5s var(--ease-ac) 0.25s, transform 0.5s var(--ease-ac) 0.25s;
}
.slide.entered .pullquote-text      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .pullquote-text { transition-delay:0s; }
/* em 标签作为高亮关键词，配荧光笔效果 */
.pullquote-text em {
  font-style: normal; color: var(--text-primary);
  background: linear-gradient(180deg, transparent 65%, rgba(25,200,185,0.32) 65%);
  padding: 0 0.1em;
}

.pullquote-author {
  margin-top: clamp(20px,3vh,32px);
  display: inline-flex; align-items: center; gap: clamp(10px,1.5vw,14px);
  padding: clamp(8px,1.2vh,12px) clamp(16px,2vw,22px);
  background: var(--bg-parchment); border-radius: var(--radius-pill);
  border: var(--border-std);
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.5s var(--ease-ac) 0.4s, transform 0.5s var(--ease-ac) 0.4s;
}
.slide.entered .pullquote-author      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .pullquote-author { transition-delay:0s; }
.pullquote-avatar {
  width: clamp(28px,3vw,36px); height: clamp(28px,3vw,36px);
  border-radius: 50%; background: var(--accent-teal);
  color: #fff; font-weight: 800; font-size: clamp(11px,1.3vw,13px);
  display: flex; align-items: center; justify-content: center;
  box-shadow: 0 3px 0 0 var(--accent-teal-a);
}
.pullquote-name {
  font-size: clamp(13px,1.5vw,16px); font-weight: 700;
  color: var(--text-body); letter-spacing: 0.02em;
}
```

### HTML 片段

```html
<section class="slide slide-pullquote" data-slide="N">
  <div class="pullquote-mark">"</div>
  <p class="pullquote-text">
    <!-- 1 句金句，<em> 包裹关键词。整段不超过 3 行 -->
  </p>
  <div class="pullquote-author">
    <div class="pullquote-avatar"><!-- 2 字缩写 --></div>
    <div class="pullquote-name"><!-- 姓名 · 职位 --></div>
  </div>
</section>
```

**密度上限**：1 引言段（≤3 行/40 字） + 1 作者卡。

---

## LAYOUT K — Stat Strip（横向多指标条）

**使用场景**：3-4 个并列指标——季度核心数据汇总、产品三维度对比、KPI 仪表盘。比 Card Grid 更纯数据导向。
**背景**：羊皮纸 `--bg-parchment`，指标间细线分隔。

### CSS

```css
.slide-statstrip {
  background: var(--bg-parchment);
  padding: clamp(32px,5vh,60px) clamp(32px,5vw,80px);
  justify-content: center;
}

.statstrip-header {
  text-align: center; margin-bottom: clamp(28px,4.5vh,48px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .statstrip-header      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .statstrip-header { transition-delay:0s; }

.statstrip-row {
  display: grid; grid-auto-flow: column; grid-auto-columns: 1fr;
  align-items: stretch;
  background: var(--bg-card); border-radius: var(--radius-card);
  box-shadow: var(--shadow-card);
  padding: clamp(20px,3vh,36px) clamp(8px,1.5vw,16px);
}

.stat-item {
  position: relative;
  display: flex; flex-direction: column; align-items: center; text-align: center;
  padding: clamp(8px,1.5vw,16px);
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .stat-item              { opacity:1; transform:translateY(0); }
.slide.entered .stat-item:nth-child(1) { transition-delay:0.18s; }
.slide.entered .stat-item:nth-child(2) { transition-delay:0.28s; }
.slide.entered .stat-item:nth-child(3) { transition-delay:0.38s; }
.slide.entered .stat-item:nth-child(4) { transition-delay:0.48s; }
.slide:not(.entered) .stat-item { transition-delay:0s; }

/* 指标之间的细线分隔 */
.stat-item + .stat-item::before {
  content: ''; position: absolute;
  top: 15%; bottom: 15%; left: 0;
  width: 1px; background: var(--text-secondary); opacity: 0.25;
}

.stat-item-num {
  font-size: clamp(40px,6.5vw,88px); font-weight: 900;
  color: var(--accent-teal); line-height: 1; letter-spacing: -0.02em;
  text-shadow: 0 4px 0 rgba(25,200,185,0.15);
  margin-bottom: clamp(8px,1.4vh,14px);
}
.stat-item-num .unit   { font-size: 0.45em; font-weight: 800; color: var(--text-secondary); margin-left: 0.04em; }
.stat-item-num .prefix { font-size: 0.5em; vertical-align: top; line-height: 1; color: var(--accent-teal); }

.stat-item-label {
  font-size: clamp(12px,1.4vw,15px); font-weight: 700;
  color: var(--text-primary); line-height: 1.3;
  margin-bottom: 4px;
}
.stat-item-trend {
  font-size: clamp(10px,1.1vw,12px); font-weight: 600;
  color: var(--text-secondary); line-height: 1.4;
}
.stat-item-trend.up   { color: var(--green, #6fba2c); }
.stat-item-trend.down { color: #d96868; }
```

### HTML 片段

```html
<section class="slide slide-statstrip" data-slide="N">
  <div class="statstrip-header">
    <div class="slide-label"><!-- 顶部小标签 --></div>
    <h2 class="slide-title"><!-- 标题 --></h2>
  </div>

  <div class="statstrip-row">
    <!-- 每个指标；3-4 个 -->
    <div class="stat-item">
      <div class="stat-item-num"><span class="prefix">+</span>34<span class="unit">%</span></div>
      <div class="stat-item-label">营收同比</div>
      <div class="stat-item-trend up">↑ 超目标 9pp</div>
    </div>
    <!-- 重复至多 4 个 -->
  </div>
</section>
```

**密度上限**：1 标题 + 3-4 个指标（每指标：数字 + label + 1 行 trend）。

---

## LAYOUT L — Process Flow（流程图 / 步骤箭头）

**使用场景**：3-5 个有序步骤——业务流程、产品 onboarding 流、决策树。盒子间用 AC 风手绘箭头串联。
**背景**：羊皮纸 `--bg-parchment`。

### CSS

```css
.slide-process {
  background: var(--bg-parchment);
  padding: clamp(32px,5vh,60px) clamp(32px,5vw,80px);
  justify-content: center;
}

.process-header {
  text-align: center; margin-bottom: clamp(28px,4.5vh,48px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .process-header      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .process-header { transition-delay:0s; }

.process-row {
  display: flex; align-items: stretch; justify-content: center;
  gap: clamp(8px,1.5vw,20px);
}

.process-step {
  flex: 1 1 0; max-width: 280px;
  background: var(--bg-card); border-radius: var(--radius-card);
  padding: clamp(16px,2.2vw,24px);
  box-shadow: var(--shadow-card);
  display: flex; flex-direction: column; align-items: flex-start; gap: clamp(8px,1.4vh,12px);
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .process-step              { opacity:1; transform:translateY(0); }
.slide.entered .process-step:nth-of-type(1) { transition-delay:0.18s; }
.slide.entered .process-step:nth-of-type(2) { transition-delay:0.30s; }
.slide.entered .process-step:nth-of-type(3) { transition-delay:0.42s; }
.slide.entered .process-step:nth-of-type(4) { transition-delay:0.54s; }
.slide.entered .process-step:nth-of-type(5) { transition-delay:0.66s; }
.slide:not(.entered) .process-step { transition-delay:0s; }

.process-icon {
  width: clamp(40px,4.5vw,56px); height: clamp(40px,4.5vw,56px);
  border-radius: var(--radius-sm);
  display: flex; align-items: center; justify-content: center;
  box-shadow: 0 3px 0 0 rgba(0,0,0,0.1);
}

.process-step-num {
  font-size: clamp(10px,1.1vw,12px); font-weight: 800;
  color: var(--text-secondary); letter-spacing: 0.15em; text-transform: uppercase;
}
.process-step-title {
  font-size: clamp(14px,1.7vw,19px); font-weight: 800;
  color: var(--text-primary); line-height: 1.25;
}
.process-step-desc {
  font-size: clamp(11px,1.2vw,13px); font-weight: 500;
  color: var(--text-secondary); line-height: 1.5;
}

/* 步骤间箭头 — 用纯 CSS 三角形 + 线段画出 AC 风手绘感 */
.process-arrow {
  flex: 0 0 auto; align-self: center;
  display: flex; align-items: center; justify-content: center;
  color: var(--accent-teal);
  opacity: 0;
  transition: opacity 0.4s var(--ease-ac);
}
.slide.entered .process-arrow            { opacity:0.75; }
.slide.entered .process-arrow:nth-of-type(1) { transition-delay:0.24s; }
.slide.entered .process-arrow:nth-of-type(2) { transition-delay:0.36s; }
.slide.entered .process-arrow:nth-of-type(3) { transition-delay:0.48s; }
.slide.entered .process-arrow:nth-of-type(4) { transition-delay:0.60s; }
.process-arrow svg {
  width: clamp(20px,2.5vw,32px); height: clamp(20px,2.5vw,32px);
}
```

### HTML 片段

```html
<section class="slide slide-process" data-slide="N">
  <div class="process-header">
    <div class="slide-label"><!-- 顶部小标签 --></div>
    <h2 class="slide-title"><!-- 流程标题 --></h2>
  </div>

  <div class="process-row">
    <!-- 步骤盒 + 箭头交替，最多 5 步 -->
    <div class="process-step">
      <div class="process-icon" style="background: var(--card-teal);">
        <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="#fff" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><!-- icon --></svg>
      </div>
      <div class="process-step-num">STEP 01</div>
      <div class="process-step-title"><!-- 步骤名 --></div>
      <div class="process-step-desc"><!-- 1 句话说明 --></div>
    </div>

    <div class="process-arrow">
      <svg viewBox="0 0 32 32" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
        <path d="M4 16 Q16 8 28 16"/><polyline points="22 11 28 16 22 21"/>
      </svg>
    </div>

    <div class="process-step">
      <div class="process-icon" style="background: var(--card-yellow);"><!-- ... --></div>
      <div class="process-step-num">STEP 02</div>
      <div class="process-step-title"><!-- 步骤名 --></div>
      <div class="process-step-desc"><!-- 1 句话说明 --></div>
    </div>
    <!-- ...重复至多 5 步，箭头数 = 步数 - 1 -->
  </div>
</section>
```

**密度上限**：1 标题 + 3-5 个步骤（每步骤：图标 + 编号 + 标题 + ≤30 字说明）；步骤间手绘箭头。

---

## LAYOUT M — Image Hero（图片为主）

**使用场景**：截图展示、产品照片、信息图——任何"图片本身就是主角"的页。文字简短作配。
**背景**：羊皮纸 `--bg-parchment`，图片用 `--radius-organic` 不规则圆角。

### CSS

```css
.slide-imghero {
  background: var(--bg-parchment);
  padding: clamp(24px,4vh,48px) clamp(32px,5vw,80px);
  align-items: center; justify-content: center;
  gap: clamp(16px,2.5vh,28px);
}

.imghero-frame {
  width: 100%; max-width: min(80vw, 900px);
  max-height: min(60vh, 500px);
  border-radius: var(--radius-organic);
  box-shadow: var(--shadow-card);
  overflow: hidden;
  background: var(--bg-card);
  opacity: 0; transform: scale(0.94);
  transition: opacity 0.6s var(--ease-ac) 0.15s, transform 0.6s var(--ease-ac) 0.15s;
}
.slide.entered .imghero-frame      { opacity:1; transform:scale(1); }
.slide:not(.entered) .imghero-frame { transition-delay:0s; }
.imghero-frame img {
  display: block; width: 100%; height: 100%;
  max-height: min(60vh, 500px);
  object-fit: cover;
}

.imghero-caption {
  text-align: center; max-width: min(85vw, 800px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.45s var(--ease-ac) 0.35s, transform 0.45s var(--ease-ac) 0.35s;
}
.slide.entered .imghero-caption      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .imghero-caption { transition-delay:0s; }

.imghero-caption .slide-label  { color: var(--accent-teal); margin-bottom: 6px; }
.imghero-caption .slide-title  {
  font-size: clamp(20px,2.6vw,32px); font-weight: 800;
  margin-bottom: clamp(4px,1vh,8px);
}
.imghero-desc {
  font-size: clamp(12px,1.4vw,16px); font-weight: 500;
  color: var(--text-secondary); line-height: 1.55;
}
```

### HTML 片段

```html
<section class="slide slide-imghero" data-slide="N">
  <div class="imghero-frame">
    <img src="assets/your-image.png" alt="<!-- 图片描述 -->">
  </div>
  <div class="imghero-caption">
    <div class="slide-label"><!-- 顶部小标签，可省略 --></div>
    <h2 class="slide-title"><!-- 图片主题 --></h2>
    <p class="imghero-desc"><!-- 1-2 句说明，可省略 --></p>
  </div>
</section>
```

**密度上限**：1 图（≤60vh）+ 1 caption（label + title + ≤2 行 desc）。如需在文字旁放图，请改用 Two Column。

---

## LAYOUT N — Code Slide（代码片段 / 技术分享）

**使用场景**：技术分享、代码 walkthrough、配置示例。零依赖语法高亮——用内联 `<span>` class 手动标注关键词，CSS 染色。
**背景**：羊皮纸 `--bg-parchment`，代码卡片暖米色 `--bg-card`。

### CSS

```css
.slide-code {
  background: var(--bg-parchment);
  padding: clamp(24px,4vh,48px) clamp(32px,5vw,80px);
  justify-content: center; gap: clamp(16px,2.5vh,24px);
}

.code-header {
  text-align: center;
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .code-header      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .code-header { transition-delay:0s; }

.code-card {
  width: 100%; max-width: min(85vw, 1000px);
  margin: 0 auto;
  background: var(--bg-card); border-radius: var(--radius-card);
  box-shadow: var(--shadow-card);
  overflow: hidden;
  opacity: 0; transform: translateY(16px) scale(0.98);
  transition: opacity 0.5s var(--ease-ac) 0.2s, transform 0.5s var(--ease-ac) 0.2s;
}
.slide.entered .code-card      { opacity:1; transform:translateY(0) scale(1); }
.slide:not(.entered) .code-card { transition-delay:0s; }

.code-filebar {
  display: flex; align-items: center; gap: clamp(8px,1.2vw,14px);
  padding: clamp(10px,1.4vh,14px) clamp(14px,1.8vw,22px);
  background: var(--text-primary);
  color: var(--bg-card);
}
.code-dots { display: flex; gap: 6px; }
.code-dots span {
  width: 10px; height: 10px; border-radius: 50%;
  background: var(--bg-card); opacity: 0.45;
}
.code-dots span:nth-child(1) { background: #ff6b5b; opacity: 0.85; }
.code-dots span:nth-child(2) { background: #ffcc55; opacity: 0.85; }
.code-dots span:nth-child(3) { background: #5fd17e; opacity: 0.85; }
.code-filename {
  flex: 1;
  font-family: ui-monospace, 'SF Mono', Menlo, Consolas, monospace;
  font-size: clamp(12px,1.3vw,14px); font-weight: 600;
  letter-spacing: 0.02em; text-align: center;
}
.code-lang {
  padding: 3px 10px; border-radius: var(--radius-pill);
  background: var(--accent-teal); color: #fff;
  font-size: clamp(9px,1vw,11px); font-weight: 800;
  letter-spacing: 0.1em; text-transform: uppercase;
}

.code-block {
  margin: 0; padding: clamp(16px,2.2vh,24px) clamp(16px,2vw,28px);
  font-family: ui-monospace, 'SF Mono', Menlo, Consolas, monospace;
  font-size: clamp(11px,1.3vw,15px); line-height: 1.7;
  color: var(--text-body); white-space: pre;
  overflow-x: auto;
  counter-reset: codeline;
}
.code-block .line {
  counter-increment: codeline;
  display: block;
}
.code-block .line::before {
  content: counter(codeline);
  display: inline-block;
  width: 2.5ch; margin-right: 1.5em;
  text-align: right;
  color: var(--text-secondary); opacity: 0.45;
  user-select: none;
}

/* 语法高亮 token —— 全部使用 AC 调色板 */
.code-block .kw  { color: var(--accent-teal); font-weight: 700; }   /* 关键字 if/return/const */
.code-block .fn  { color: var(--text-primary); font-weight: 700; }  /* 函数名 */
.code-block .str { color: var(--green, #6fba2c); }                  /* 字符串 */
.code-block .num { color: var(--accent-yellow-d); }                 /* 数字 */
.code-block .cm  { color: var(--text-muted); font-style: italic; }  /* 注释 */
.code-block .pn  { color: var(--text-secondary); }                  /* 标点 */

.code-caption {
  text-align: center; max-width: min(80vw, 800px);
  margin: 0 auto;
  font-size: clamp(12px,1.4vw,15px); font-weight: 500;
  color: var(--text-secondary); line-height: 1.55;
  opacity: 0; transform: translateY(10px);
  transition: opacity 0.45s var(--ease-ac) 0.4s, transform 0.45s var(--ease-ac) 0.4s;
}
.slide.entered .code-caption      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .code-caption { transition-delay:0s; }
```

### HTML 片段

```html
<section class="slide slide-code" data-slide="N">
  <div class="code-header">
    <div class="slide-label"><!-- 顶部小标签，如"代码示例" --></div>
    <h2 class="slide-title"><!-- 标题 --></h2>
  </div>

  <div class="code-card">
    <div class="code-filebar">
      <div class="code-dots"><span></span><span></span><span></span></div>
      <div class="code-filename"><!-- 文件名，如 useDebounce.ts --></div>
      <div class="code-lang"><!-- 语言，如 TypeScript --></div>
    </div>

    <!-- 用 <span class="line"> 包每行；token 用 .kw/.fn/.str/.num/.cm/.pn 标注。
         每行末尾必须留 \n，否则换行会丢失（white-space: pre）。
         不要在 line 外加空格/换行，会破坏对齐。 -->
    <pre class="code-block"><code><span class="line"><span class="kw">import</span> { useState, useEffect } <span class="kw">from</span> <span class="str">'react'</span>;
</span><span class="line">
</span><span class="line"><span class="kw">export function</span> <span class="fn">useDebounce</span>(value, delay = <span class="num">500</span>) {
</span><span class="line">  <span class="kw">const</span> [debounced, setDebounced] = <span class="fn">useState</span>(value);
</span><span class="line">
</span><span class="line">  <span class="fn">useEffect</span>(() => {
</span><span class="line">    <span class="kw">const</span> id = <span class="fn">setTimeout</span>(() => <span class="fn">setDebounced</span>(value), delay);
</span><span class="line">    <span class="kw">return</span> () => <span class="fn">clearTimeout</span>(id);
</span><span class="line">  }, [value, delay]);
</span><span class="line">
</span><span class="line">  <span class="kw">return</span> debounced;
</span><span class="line">}
</span></code></pre>
  </div>

  <p class="code-caption"><!-- 可选 1 句话解释代码意图 --></p>
</section>
```

**密度上限**：1 标题 + 1 代码卡（≤10 行，每行 ≤80 字符）+ 1 行 caption。超出请拆分多张。

**Token 用法**：
- `.kw` — 关键字（`import`/`return`/`const`/`if`/`for` 等控制流和声明词）
- `.fn` — 函数名（调用或定义）
- `.str` — 字符串字面量
- `.num` — 数字字面量
- `.cm` — 行注释或块注释
- `.pn` — 标点（可选，默认 `--text-body` 已够柔和）

---

## LAYOUT O — NookPhone Showcase（手机化总览）

**使用场景**：章节总览、产品功能矩阵、目录页——AC 风最标志性的视觉锤。左侧 NookPhone 9 宫格，右侧文字说明。
**背景**：羊皮纸 `--bg-parchment`。

### CSS

```css
.slide-phone {
  background: var(--bg-parchment);
  padding: clamp(24px,4vh,48px) clamp(32px,5vw,72px);
  justify-content: center;
}

.phone-layout {
  display: grid; grid-template-columns: auto 1fr;
  gap: clamp(28px,4vw,64px); align-items: center;
}

/* ── 手机外壳 ─────────────────────────────────────────── */
.phone-mock {
  width: clamp(260px,28vw,400px);
  aspect-ratio: 527 / 788;
  background: #F8F4E8;
  border-radius: clamp(56px,7vw,100px);
  box-shadow: 0 10px 24px rgba(107,92,67,0.28), inset 0 0 0 4px rgba(121,79,39,0.08);
  padding: clamp(16px,2vw,28px) clamp(14px,1.8vw,24px);
  display: flex; flex-direction: column; gap: clamp(10px,1.5vh,18px);
  opacity: 0; transform: translateX(-24px);
  transition: opacity 0.55s var(--ease-ac) 0.15s, transform 0.55s var(--ease-ac) 0.15s;
}
.slide.entered .phone-mock      { opacity:1; transform:translateX(0); }
.slide:not(.entered) .phone-mock { transition-delay:0s; }

/* 顶部时钟条 */
.phone-time {
  font-weight: 800; color: #DDDBCC;
  font-size: clamp(14px,1.6vw,20px);
  letter-spacing: 0.08em; text-align: center;
  padding-top: clamp(6px,1vh,12px);
}
.phone-time .blink { animation: phoneBlink 1s steps(1) infinite; margin: 0 0.05em; }
.phone-time .ampm  { font-size: 0.7em; margin-left: 0.2em; }
@keyframes phoneBlink { 0%,50% { opacity:1; } 51%,100% { opacity:0; } }

/* 中间欢迎语 */
.phone-welcome {
  font-size: clamp(20px,2.6vw,32px); font-weight: 800;
  color: #725C4E; letter-spacing: 0.04em; text-align: center;
  margin-top: clamp(2px,0.5vh,6px);
}

/* 9 宫格 */
.phone-apps {
  display: grid; grid-template-columns: repeat(3, 1fr);
  gap: clamp(8px,1vw,14px);
  padding: clamp(4px,0.8vw,8px);
  flex: 1; align-content: center;
}
.phone-app {
  aspect-ratio: 1; border-radius: clamp(14px,1.8vw,24px);
  display: flex; align-items: center; justify-content: center;
  position: relative;
  box-shadow: 0 3px 0 0 rgba(0,0,0,0.12);
  opacity: 0.6;
  transition: all 0.3s var(--ease-ac);
}
.phone-app.active {
  opacity: 1; transform: scale(1.08) translateY(-2px);
  box-shadow: 0 6px 0 0 rgba(0,0,0,0.18);
}
.phone-app svg { width: 55%; height: 55%; stroke: #fff; }
/* 角标小红点 */
.phone-app.badge::before {
  content: ''; position: absolute; top: -3px; left: -3px;
  width: clamp(10px,1.2vw,14px); height: clamp(10px,1.2vw,14px);
  border-radius: 50%; background: #FF544A;
  border: 3px solid #F8F4E8;
}

/* 底部页码点 */
.phone-dots {
  display: flex; justify-content: center; gap: 6px;
  padding-bottom: clamp(8px,1.2vh,14px);
}
.phone-dots span {
  width: 6px; height: 6px; border-radius: 50%;
  background: #DDDBCC;
}
.phone-dots span.on { background: var(--accent-teal); }

/* ── 右侧文字区 ───────────────────────────────────────── */
.phone-content {
  opacity: 0; transform: translateX(24px);
  transition: opacity 0.5s var(--ease-ac) 0.3s, transform 0.5s var(--ease-ac) 0.3s;
}
.slide.entered .phone-content      { opacity:1; transform:translateX(0); }
.slide:not(.entered) .phone-content { transition-delay:0s; }
.phone-content .slide-label { margin-bottom: 8px; }
.phone-content .slide-title { margin-bottom: clamp(12px,2vh,20px); }
.phone-body {
  font-size: clamp(13px,1.6vw,17px); font-weight: 500;
  color: var(--text-body); line-height: 1.7;
  margin-bottom: clamp(12px,2vh,18px);
}
.phone-body strong { color: var(--text-primary); font-weight: 700; }

/* 当前 app 对应的功能名称列表（可选） */
.phone-tags { display: flex; flex-wrap: wrap; gap: clamp(6px,0.8vw,10px); }
.phone-tags li {
  list-style: none;
  padding: 4px 12px; border-radius: var(--radius-pill);
  background: var(--bg-card); border: var(--border-std);
  font-size: clamp(11px,1.2vw,13px); font-weight: 700;
  color: var(--text-muted); letter-spacing: 0.02em;
}
.phone-tags li.on {
  background: var(--accent-teal); color: #fff; border-color: var(--accent-teal-a);
}
```

### HTML 片段

```html
<section class="slide slide-phone" data-slide="N">
  <div class="phone-layout">

    <!-- 手机外壳 -->
    <div class="phone-mock">
      <div class="phone-time">10<span class="blink">:</span>42<span class="ampm">PM</span></div>
      <div class="phone-welcome">Welcome!</div>

      <div class="phone-apps">
        <!-- 9 个 app，颜色用 --card-* 变量；当前章节加 .active -->
        <div class="phone-app active badge" style="background: var(--card-pink);">
          <svg viewBox="0 0 24 24" fill="none" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><!-- icon --></svg>
        </div>
        <div class="phone-app" style="background: var(--card-blue);"><!-- ... --></div>
        <div class="phone-app" style="background: var(--card-yellow);"><!-- ... --></div>
        <div class="phone-app" style="background: var(--card-orange);"><!-- ... --></div>
        <div class="phone-app" style="background: var(--card-teal);"><!-- ... --></div>
        <div class="phone-app" style="background: var(--card-green);"><!-- ... --></div>
        <div class="phone-app" style="background: var(--card-purple,#b77dee);"><!-- ... --></div>
        <div class="phone-app" style="background: var(--card-red,#fc736d);"><!-- ... --></div>
        <div class="phone-app" style="background: var(--card-lime,#d1da49);"><!-- ... --></div>
      </div>

      <div class="phone-dots"><span class="on"></span><span></span></div>
    </div>

    <!-- 右侧文字 -->
    <div class="phone-content">
      <div class="slide-label"><!-- 顶部标签 --></div>
      <h2 class="slide-title"><!-- 标题 --></h2>
      <p class="phone-body"><!-- 1-2 段说明，<strong> 强调关键词 --></p>
      <ul class="phone-tags">
        <!-- 可选：对应 9 个 app 的功能标签；当前章节加 .on -->
        <li class="on">相机</li><li>里程</li><li>图鉴</li>
        <li>DIY</li><li>设计</li><li>地图</li>
        <li>变装</li><li>陶里</li><li>聊天</li>
      </ul>
    </div>
  </div>
</section>
```

**密度上限**：1 标题 + 9 个 app（每个 ≤6 字标签） + 2 段说明文字。手机本身就承担了视觉密度，文字应克制。

---

## 工具组件：Tab Strip（章节导航条）

**用法**：可加到任何 layout 的 header 顶部，显示整个 deck 的章节序列。当前章节高亮，其它章节淡化。**不必交互**——默认是静态指示器；如需点击跳转，给每个 pill 加 `onclick="goTo(N)"`。

### CSS

```css
.tab-strip {
  display: inline-flex; gap: clamp(4px,0.6vw,8px);
  padding: clamp(4px,0.6vh,6px);
  background: rgba(255,255,255,0.6);
  border-radius: var(--radius-pill);
  border: var(--border-std);
  margin-bottom: clamp(16px,2.5vh,28px);
  align-self: center; /* 配合 flex column 居中 */
  opacity: 0; transform: translateY(-8px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .tab-strip      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .tab-strip { transition-delay:0s; }

.tab-pill {
  position: relative;
  padding: clamp(6px,1vh,10px) clamp(12px,1.6vw,18px);
  border: none; background: transparent;
  font-family: var(--font-main);
  font-size: clamp(11px,1.2vw,13px); font-weight: 700;
  color: var(--text-muted); letter-spacing: 0.04em;
  border-radius: var(--radius-pill);
  cursor: pointer;
  transition: all 0.25s var(--ease-ac);
}
.tab-pill:hover { background: rgba(25,200,185,0.1); color: var(--text-body); }
.tab-pill.active {
  background: var(--accent-teal); color: #fff;
  font-weight: 800;
  box-shadow: 0 3px 0 0 var(--accent-teal-a);
}
/* 当前 tab 加叶子装饰（可选） */
.tab-pill.active::after {
  content: ''; position: absolute;
  top: -6px; right: -4px;
  width: 14px; height: 14px;
  background: var(--green, #6fba2c);
  clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);
  transform: rotate(15deg);
  animation: leafWiggle 2.5s ease-in-out infinite;
}
@keyframes leafWiggle {
  0%, 100% { transform: rotate(15deg); }
  50%      { transform: rotate(-10deg); }
}
```

### HTML 用法

加在任意 layout 的 header 部分，**取代或加在 `.slide-label` 之上**：

```html
<div class="tab-strip" role="navigation">
  <button class="tab-pill">现状</button>
  <button class="tab-pill active">方案</button>
  <button class="tab-pill">数据</button>
  <button class="tab-pill">计划</button>
</div>
```

**最佳实践**：
- 4-6 个章节为宜，超过 6 个挤
- 整个 deck 的所有同章节幻灯片用**同一份** `.tab-strip`（只改 `.active` 的位置）
- 章节名 ≤4 字

---

## LAYOUT P — FAQ / Q&A（问答列表）

**使用场景**：产品 FAQ、技术分享的常见问题、复盘的"为什么这么做"。3-4 组问答纵向堆叠。
**背景**：羊皮纸 `--bg-parchment`。
**强风格要求**：Q/A 要像留言板或公告板上的上下两层纸条，而不是普通 FAQ 列表；建议在页面底边补 `栅栏 / 小屋 / 邮筒` 中的至少一个。

### CSS

```css
.slide-faq {
  background: var(--bg-parchment);
  padding: clamp(28px,4vh,52px) clamp(32px,5vw,80px);
  justify-content: center;
}

.faq-header {
  margin-bottom: clamp(20px,3vh,32px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.4s var(--ease-ac) 0.05s, transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .faq-header      { opacity:1; transform:translateY(0); }
.slide:not(.entered) .faq-header { transition-delay:0s; }

.faq-list {
  display: flex; flex-direction: column; gap: clamp(14px,2vh,22px);
  max-width: min(95vw, 1100px); margin: 0 auto;
}

.faq-item {
  display: flex; flex-direction: column; gap: clamp(8px,1.2vh,12px);
  opacity: 0; transform: translateY(14px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .faq-item              { opacity:1; transform:translateY(0); }
.slide.entered .faq-item:nth-child(1) { transition-delay:0.18s; }
.slide.entered .faq-item:nth-child(2) { transition-delay:0.30s; }
.slide.entered .faq-item:nth-child(3) { transition-delay:0.42s; }
.slide.entered .faq-item:nth-child(4) { transition-delay:0.54s; }
.slide:not(.entered) .faq-item { transition-delay:0s; }

.faq-q, .faq-a {
  display: flex; gap: clamp(10px,1.4vw,16px);
  padding: clamp(12px,1.8vh,18px) clamp(16px,2vw,22px);
  border-radius: var(--radius-card);
  align-items: flex-start;
}
.faq-q {
  background: var(--bg-card);
  border-left: 4px solid var(--accent-teal);
  box-shadow: var(--shadow-card);
}
.faq-a {
  margin-left: clamp(28px,4vw,56px);
  background: transparent;
  border-left: 4px solid var(--accent-yellow);
}

.faq-badge {
  flex-shrink: 0;
  width: clamp(28px,3.2vw,38px); height: clamp(28px,3.2vw,38px);
  border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  font-size: clamp(13px,1.5vw,17px); font-weight: 900;
  color: #fff; letter-spacing: 0.04em;
}
.faq-q .faq-badge { background: var(--accent-teal); box-shadow: 0 3px 0 0 var(--accent-teal-a); }
.faq-a .faq-badge { background: var(--accent-yellow); color: var(--text-body); box-shadow: 0 3px 0 0 var(--accent-yellow-d); }

.faq-q-text {
  font-size: clamp(14px,1.7vw,19px); font-weight: 800;
  color: var(--text-primary); line-height: 1.4;
  padding-top: 0.2em;
}
.faq-a-text {
  font-size: clamp(12px,1.4vw,15px); font-weight: 500;
  color: var(--text-body); line-height: 1.6;
  padding-top: 0.35em;
}
.faq-a-text strong { color: var(--text-primary); font-weight: 700; }
```

### HTML 片段

```html
<section class="slide slide-faq" data-slide="N">
  <div class="faq-header">
    <div class="slide-label"><!-- 顶部小标签 --></div>
    <h2 class="slide-title"><!-- FAQ 主题 --></h2>
  </div>

  <div class="faq-list">
    <!-- 每组 Q+A，最多 4 组 -->
    <div class="faq-item">
      <div class="faq-q">
        <div class="faq-badge">Q</div>
        <div class="faq-q-text"><!-- 问题，≤25 字 --></div>
      </div>
      <div class="faq-a">
        <div class="faq-badge">A</div>
        <div class="faq-a-text"><!-- 答案，≤60 字。<strong> 强调结论 --></div>
      </div>
    </div>
    <!-- 重复至多 4 组 -->
  </div>
</section>
```

**密度上限**：1 标题 + 3-4 组 Q+A（每 Q ≤25 字，每 A ≤60 字）。

---

## LAYOUT Q — Island Scene（岛屿场景 / Ambient Hero）

**使用场景**：章节大开场、"故事开始"、结尾抒情页、产品愿景页。比 Cover 更有沉浸感，整张幻灯片以一幅可呼吸的岛屿插画作底，文字漂浮在水面之上。
**背景**：天空 → 远水 → 沙滩四段渐变。

### CSS

```css
.slide-scene {
  background: linear-gradient(180deg, #b4e4f2 0%, #d8ecf0 45%, #f0eed5 75%, var(--bg-parchment) 100%);
  align-items: center;
  text-align: center;
  padding: clamp(40px,7vh,90px) clamp(24px,5vw,80px) 0;
}

/* 云 & 鸟（天空装饰）*/
.scene-cloud, .scene-bird { position: absolute; pointer-events: none; }
.scene-cloud { opacity: 0.85; animation: cloudDrift 14s ease-in-out infinite; }
.scene-cloud-1 { top: clamp(8%,12vh,18%);  left: 6%;  width: clamp(80px,11vw,140px); }
.scene-cloud-2 { top: clamp(14%,18vh,24%); right: 8%; width: clamp(60px,9vw,110px); animation-delay: -7s; }
@keyframes cloudDrift {
  0%,100% { transform: translateX(0); }
  50%     { transform: translateX(20px); }
}

.scene-bird {
  opacity: 0.55; color: var(--text-muted);
  animation: birdFly 7s ease-in-out infinite;
}
.scene-bird-1 { top: 20%; left: 24%;  width: clamp(28px,3.5vw,40px); }
.scene-bird-2 { top: 26%; right: 28%; width: clamp(24px,3vw,34px); animation-delay: -3.5s; }
@keyframes birdFly {
  0%,100% { transform: translate(0,0); }
  50%     { transform: translate(36px,-8px); }
}

/* 主内容（标题块在岛屿之上）*/
.scene-content {
  position: relative; z-index: 2;
  margin-top: clamp(20px,5vh,60px);
  opacity: 0; transform: translateY(16px);
  transition: opacity 0.55s var(--ease-ac), transform 0.55s var(--ease-ac);
}
.scene-content > * + * { margin-top: clamp(10px,1.8vh,18px); }
.slide.entered .scene-content      { opacity: 1; transform: translateY(0); transition-delay: 0.18s; }
.slide:not(.entered) .scene-content { transition-delay: 0s; }

.scene-eyebrow {
  display: inline-block;
  padding: 6px 18px;
  background: var(--bg-card); border-radius: var(--radius-pill);
  box-shadow: var(--shadow-btn);
  font-size: clamp(10px,1.2vw,13px); font-weight: 700;
  color: var(--text-muted); letter-spacing: 0.12em; text-transform: uppercase;
}
.scene-title {
  font-size: clamp(32px,6vw,76px); font-weight: 900;
  color: var(--text-primary); line-height: 1.08; letter-spacing: -0.01em;
  text-shadow: 0 3px 0 rgba(255,255,255,0.5);
}
.scene-title em { font-style: normal; color: var(--accent-teal); }
.scene-caption {
  font-size: clamp(14px,1.8vw,19px); font-weight: 600;
  color: var(--text-body); line-height: 1.5;
  max-width: 560px; margin-left: auto; margin-right: auto;
}

/* 岛屿插画，绝对定位到底部 */
.scene-stage {
  position: absolute; left: 50%; bottom: -2px;
  transform: translateX(-50%);
  width: min(720px, 88%);
  pointer-events: none; z-index: 1;
}

/* 树摇摆（SVG transform-box 保证 transform-origin 用元素自身的盒子）*/
.scene-tree {
  transform-box: fill-box; transform-origin: 50% 90%;
  animation: treeSway 5.5s ease-in-out infinite;
}
@keyframes treeSway {
  0%,100% { transform: rotate(-1.4deg); }
  50%     { transform: rotate(1.4deg); }
}

/* 水波横向漂移 */
.scene-wave   { animation: waveDrift 3.8s ease-in-out infinite; }
.scene-wave-b { animation-delay: -1.9s; }
@keyframes waveDrift {
  0%,100% { transform: translateX(0); }
  50%     { transform: translateX(-14px); }
}

@media (prefers-reduced-motion: reduce) {
  .scene-cloud, .scene-bird, .scene-tree, .scene-wave { animation: none; }
}
```

### HTML 片段

```html
<section class="slide slide-scene" data-slide="N">
  <!-- 天空：云 -->
  <svg class="scene-cloud scene-cloud-1" viewBox="0 0 140 50" fill="#fff" aria-hidden="true">
    <ellipse cx="35"  cy="30" rx="30" ry="16"/>
    <ellipse cx="78"  cy="25" rx="34" ry="20"/>
    <ellipse cx="115" cy="32" rx="22" ry="14"/>
  </svg>
  <svg class="scene-cloud scene-cloud-2" viewBox="0 0 120 40" fill="#fff" aria-hidden="true">
    <ellipse cx="28" cy="24" rx="22" ry="13"/>
    <ellipse cx="66" cy="20" rx="28" ry="16"/>
    <ellipse cx="98" cy="26" rx="18" ry="11"/>
  </svg>

  <!-- 天空：远处的小鸟 -->
  <svg class="scene-bird scene-bird-1" viewBox="0 0 40 18" aria-hidden="true">
    <path d="M2 14 Q10 3 19 12 Q26 3 38 14" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
  </svg>
  <svg class="scene-bird scene-bird-2" viewBox="0 0 40 18" aria-hidden="true">
    <path d="M2 14 Q10 3 19 12 Q26 3 38 14" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
  </svg>

  <!-- 标题文字 -->
  <div class="scene-content">
    <div class="scene-eyebrow"><!-- 章节号 / "下一章" / "PROLOGUE" --></div>
    <h2 class="scene-title"><!-- 主标题，<em> 包裹高亮词 --></h2>
    <p class="scene-caption"><!-- 1-2 行描述 --></p>
  </div>

  <!-- 岛屿插画 -->
  <svg class="scene-stage" viewBox="0 0 640 440" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
    <!-- 远水（淡蓝带）-->
    <path class="scene-wave scene-wave-b" d="M0 360 Q160 350 320 360 T640 360 L640 410 L0 410 Z" fill="#bae4f0"/>

    <!-- 三角海浪（AC 标志性视觉）-->
    <g fill="#2ec3ec" class="scene-wave">
      <path d="M40 365 L60 340 L80 365 Z"/>
      <path d="M120 365 L140 340 L160 365 Z"/>
      <path d="M480 365 L500 340 L520 365 Z"/>
      <path d="M560 365 L580 340 L600 365 Z"/>
    </g>

    <!-- 沙滩 -->
    <ellipse cx="320" cy="370" rx="240" ry="55" fill="#fed09d"/>
    <path d="M150 372 Q160 410 200 416 Q260 420 320 420 Q400 418 460 416 Q495 410 490 372 Z" fill="#fbb381" opacity="0.96"/>

    <!-- 草地（圆顶岛丘）-->
    <path d="M150 370 Q170 280 240 270 Q310 260 380 272 Q440 285 490 370 Z" fill="#8cc751"/>

    <!-- 草地装饰三角 -->
    <path d="M210 340 L226 322 L240 348 Z" fill="#78bb4d"/>
    <path d="M280 320 L296 302 L312 328 Z" fill="#78bb4d"/>
    <path d="M380 318 L396 300 L412 326 Z" fill="#78bb4d"/>
    <path d="M440 342 L454 326 L470 350 Z" fill="#78bb4d"/>

    <!-- 椰子树（摇摆）-->
    <g class="scene-tree">
      <!-- 树干 -->
      <path d="M312 330 Q307 270 310 220 Q302 200 318 178 Q334 200 326 220 Q329 270 322 330 Z" fill="#e5b13b"/>
      <path d="M315 268 Q321 256 322 244" stroke="#c89630" stroke-width="2.5" fill="none" stroke-linecap="round" opacity="0.55"/>
      <path d="M316 220 Q322 208 320 196" stroke="#c89630" stroke-width="2"  fill="none" stroke-linecap="round" opacity="0.5"/>
      <!-- 叶冠 -->
      <ellipse cx="318" cy="150" rx="82" ry="50" fill="#8cc751"/>
      <ellipse cx="282" cy="138" rx="38" ry="30" fill="#ABD25E"/>
      <ellipse cx="350" cy="140" rx="40" ry="32" fill="#ABD25E"/>
      <ellipse cx="318" cy="115" rx="34" ry="28" fill="#ABD25E"/>
      <!-- 叶面高光 -->
      <ellipse cx="294" cy="128" rx="13" ry="9" fill="#bbd86a" opacity="0.75"/>
      <ellipse cx="336" cy="132" rx="11" ry="8" fill="#bbd86a" opacity="0.75"/>
    </g>
  </svg>
</section>
```

**密度上限**：1 eyebrow（≤12 字） + 1 主标题（≤16 字，含 `<em>` 高亮） + 1 caption（≤40 字）。文字仅承担"破题"作用，主视觉是岛屿。

---

## LAYOUT V — Color Wheel（角色色盘 / Palette Honeycomb）

**使用场景**：团队成员介绍、品牌色板、分类目录、人格 / 角色总览。把 animal-island-ui 的 13 种 Card 配色全部用上，做出"一眼记住所有人 / 所有分类"的视觉冲击。
**背景**：羊皮纸 `--bg-parchment`，蜂巢状陈列的圆形彩色徽章。

### CSS

```css
.slide-wheel {
  background: var(--bg-parchment);
  padding: clamp(28px,4.5vh,52px) clamp(28px,5vw,72px) clamp(40px,6vh,72px);
  justify-content: center; align-items: center;
}

/* 标题（复用 slide-header 视觉，但居中）*/
.wheel-header {
  text-align: center;
  margin-bottom: clamp(18px,3vh,32px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .wheel-header      { opacity: 1; transform: translateY(0); transition-delay: 0.05s; }
.slide:not(.entered) .wheel-header { transition-delay: 0s; }

.wheel-label {
  font-size: clamp(10px,1.2vw,12px); font-weight: 700;
  color: var(--accent-teal); letter-spacing: 0.12em; text-transform: uppercase;
  margin-bottom: 6px;
}
.wheel-title {
  font-size: clamp(24px,3.5vw,46px); font-weight: 900;
  color: var(--text-primary); line-height: 1.15;
}

/* 蜂巢容器 */
.wheel-grid {
  display: flex; flex-direction: column;
  gap: clamp(8px,1.5vh,18px);
  align-items: center;
  width: 100%; max-width: 1000px;
}
.wheel-row {
  display: flex;
  gap: clamp(10px,1.4vw,20px);
  justify-content: center;
}
/* 第二排向右偏移半个 chip，形成蜂巢 */
.wheel-row.offset { margin-left: clamp(40px,5vw,70px); }

/* 圆形徽章 */
.wheel-chip {
  width: clamp(64px,9vw,110px);
  height: clamp(64px,9vw,110px);
  border-radius: 50%;
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  text-align: center; padding: 6px;
  box-shadow: 0 5px 0 0 rgba(0,0,0,0.12), inset 0 0 0 3px rgba(255,255,255,0.55);
  opacity: 0; transform: scale(0.6);
  transition:
    opacity 0.4s var(--ease-ac),
    transform 0.4s var(--ease-ac),
    box-shadow 0.25s var(--ease-ac);
  cursor: default;
}
.wheel-chip:hover {
  transform: translateY(-4px) scale(1.04);
  box-shadow: 0 9px 0 0 rgba(0,0,0,0.14), inset 0 0 0 3px rgba(255,255,255,0.7);
}

/* 入场逐个弹出 — 用 inline --i 控制顺序 */
.slide.entered .wheel-chip {
  opacity: 1; transform: scale(1);
  transition-delay: calc(0.08s + var(--i, 0) * 0.045s);
}
.slide:not(.entered) .wheel-chip { transition-delay: 0s; }

/* 13 种 AC Card 配色（与 animal-island-ui 一致）*/
.chip-pink         { background: #f8a6b2; color: #fff; }
.chip-purple       { background: #b77dee; color: #fff; }
.chip-blue         { background: #889df0; color: #fff; }
.chip-yellow       { background: #f7cd67; color: var(--text-body); }
.chip-orange       { background: #e59266; color: #fff; }
.chip-teal         { background: #82d5bb; color: #fff; }
.chip-green        { background: #8ac68a; color: #fff; }
.chip-red          { background: #fc736d; color: #fff; }
.chip-lime         { background: #d1da49; color: #3d5a1a; }
.chip-yellow-green { background: #ecdf52; color: var(--text-body); }
.chip-brown        { background: #9a835a; color: #fff; }
.chip-peach        { background: #e18c6f; color: #fff; }
.chip-default      { background: rgb(247,243,223); color: var(--text-body); box-shadow: 0 5px 0 0 rgba(0,0,0,0.12), inset 0 0 0 3px rgba(159,146,125,0.35); }

.chip-label {
  font-size: clamp(11px,1.3vw,15px); font-weight: 800;
  line-height: 1.15; letter-spacing: 0.02em;
}
.chip-sub {
  font-size: clamp(9px,1vw,11px); font-weight: 600;
  opacity: 0.85; margin-top: 2px; line-height: 1.2;
}

/* 底部注脚（可选）*/
.wheel-caption {
  margin-top: clamp(18px,3vh,30px);
  font-size: clamp(13px,1.5vw,16px); font-weight: 600;
  color: var(--text-body); text-align: center;
  max-width: 720px;
  opacity: 0; transform: translateY(10px);
  transition: opacity 0.4s var(--ease-ac) 0.55s, transform 0.4s var(--ease-ac) 0.55s;
}
.slide.entered .wheel-caption      { opacity: 1; transform: translateY(0); }
.slide:not(.entered) .wheel-caption { transition-delay: 0s; }

@media (max-height: 700px) {
  .wheel-chip { width: clamp(56px,7vw,88px); height: clamp(56px,7vw,88px); }
}
```

### HTML 片段

```html
<section class="slide slide-wheel" data-slide="N">
  <div class="wheel-header">
    <div class="wheel-label"><!-- 顶部小标签 --></div>
    <h2 class="wheel-title"><!-- 标题 --></h2>
  </div>

  <div class="wheel-grid">
    <!-- 第一排 4 个 -->
    <div class="wheel-row">
      <div class="wheel-chip chip-pink"   style="--i:0;"><div class="chip-label"><!-- 标签 --></div><div class="chip-sub"><!-- 副标签 --></div></div>
      <div class="wheel-chip chip-purple" style="--i:1;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-blue"   style="--i:2;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-yellow" style="--i:3;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
    </div>

    <!-- 第二排 5 个，蜂巢偏移 -->
    <div class="wheel-row offset">
      <div class="wheel-chip chip-orange" style="--i:4;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-teal"   style="--i:5;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-green"  style="--i:6;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-red"    style="--i:7;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-lime"   style="--i:8;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
    </div>

    <!-- 第三排 4 个 -->
    <div class="wheel-row">
      <div class="wheel-chip chip-yellow-green" style="--i:9;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-brown"        style="--i:10;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-peach"        style="--i:11;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
      <div class="wheel-chip chip-default"      style="--i:12;"><div class="chip-label">…</div><div class="chip-sub">…</div></div>
    </div>
  </div>

  <p class="wheel-caption"><!-- 可选 1 行说明，例如"每个色彩对应一个角色 / 类别" --></p>
</section>
```

**密度上限**：1 标题 + 4-13 个 chip（每 chip：label ≤6 字 + sub ≤8 字） + 1 行可选注脚。chip 少于 7 个时只用前 2 排（4+3）；7-12 个用 4+5+3 或 4+4+4；满 13 个走 4+5+4 蜂巢。`--i` 序号必须从 0 顺序递增。

---

## LAYOUT R — Dialogue Modal（NPC 对话框 / Game Dialog）

**使用场景**：用户原话/客户证言、产品哲学、品牌口吻金句、故事开场白。和 Pull Quote（Layout J）的区别：J 是大字金句"打到读者脸上"；R 是 NPC 在镜头里说话——角色感和情境感更强。
**背景**：天空-草地-沙滩的渐变剪影，把读者放进游戏画面里。
**关键视觉**：直接复用 animal-island-ui Modal 的标志性 blob clip-path，配合左上角"姓名牌 + 头像"+ 右下角闪烁继续箭头。

### CSS

```css
.slide-dialog {
  background: linear-gradient(180deg, #c8e8d6 0%, #d8eedf 40%, #ecdca8 75%, #e5b88a 100%);
  align-items: center;
  justify-content: flex-end;
  padding: clamp(40px,5vh,70px) clamp(40px,7vw,120px) clamp(60px,9vh,110px);
}

/* 背景：远处草丘剪影 */
.slide-dialog::before {
  content: '';
  position: absolute;
  bottom: 38%; left: 0; right: 0;
  height: 24%;
  background:
    radial-gradient(ellipse 30% 100% at 22% 100%, #abd25e 0%, transparent 78%),
    radial-gradient(ellipse 38% 100% at 76% 100%, #abd25e 0%, transparent 78%);
  opacity: 0.55;
  pointer-events: none;
}

/* 对话框（Modal 风格 blob clip-path）*/
.dialog-box {
  width: 100%; max-width: 880px;
  background: rgb(247, 243, 223);
  clip-path: url(#animal-dialog-clip);
  padding: clamp(44px,5.5vw,72px) clamp(44px,5.5vw,72px) clamp(56px,7vh,80px);
  position: relative;
  opacity: 0; transform: scale(0.92) translateY(40px);
  transition: opacity 0.55s var(--ease-ac), transform 0.55s var(--ease-ac);
}
.slide.entered .dialog-box      { opacity: 1; transform: scale(1) translateY(0); transition-delay: 0.12s; }
.slide:not(.entered) .dialog-box { transition-delay: 0s; }

/* 左上角说话者牌（横跨对话框边缘）*/
.dialog-speaker {
  position: absolute;
  top: 0; left: clamp(36px,5vw,60px);
  display: flex; align-items: center;
  gap: clamp(10px,1.5vw,16px);
  background: var(--accent-yellow);
  padding: 6px clamp(16px,2.2vw,24px) 6px 6px;
  border-radius: var(--radius-pill);
  box-shadow: 0 4px 0 0 var(--accent-yellow-d);
  z-index: 2;
  opacity: 0; transform: translate(-12px,-40%);
  transition: opacity 0.5s var(--ease-ac), transform 0.5s var(--ease-ac);
}
.slide.entered .dialog-speaker      { opacity: 1; transform: translate(0,-50%); transition-delay: 0.32s; }
.slide:not(.entered) .dialog-speaker { transition-delay: 0s; }

.speaker-avatar {
  width: clamp(36px,4.5vw,52px); height: clamp(36px,4.5vw,52px);
  border-radius: 50%;
  background: var(--accent-teal);
  display: flex; align-items: center; justify-content: center;
  font-size: clamp(13px,1.6vw,18px); font-weight: 900;
  color: #fff;
  border: 3px solid #fff;
  flex-shrink: 0;
}
.speaker-name {
  display: flex; flex-direction: column;
  font-size: clamp(13px,1.5vw,16px); font-weight: 800;
  color: var(--text-primary); line-height: 1.15;
}
.speaker-name small {
  font-size: clamp(10px,1.1vw,12px); font-weight: 600;
  color: var(--text-muted);
  letter-spacing: 0.06em;
  margin-top: 2px;
}

/* 对话文本 */
.dialog-text {
  font-size: clamp(18px,2.4vw,30px); font-weight: 600;
  line-height: 1.65;
  color: var(--text-body);
  letter-spacing: 0.01em;
  text-align: left;
  margin-top: clamp(8px,1.5vh,16px);
}
.dialog-text > p + p { margin-top: clamp(8px,1.4vh,14px); }
.dialog-text strong { color: var(--accent-teal); font-weight: 800; }
.dialog-text em { font-style: normal; color: var(--text-primary); font-weight: 700; }

/* 右下角继续指示器（闪烁向下箭头）*/
.dialog-cursor {
  position: absolute;
  bottom: clamp(18px,2.5vh,28px); right: clamp(38px,5vw,60px);
  width: 0; height: 0;
  border-left: 9px solid transparent;
  border-right: 9px solid transparent;
  border-top: 14px solid var(--text-muted);
  opacity: 0;
}
.slide.entered .dialog-cursor { animation: dialogBlink 1.4s ease-in-out 1s infinite; }
@keyframes dialogBlink {
  0%,100% { opacity: 0.35; transform: translateY(0); }
  50%     { opacity: 0.95; transform: translateY(5px); }
}
```

### HTML 片段

```html
<section class="slide slide-dialog" data-slide="N">
  <!-- Modal blob clip-path 定义（每张 dialog 幻灯片自带也安全：相同 id 浏览器忽略重复）-->
  <svg width="0" height="0" style="position:absolute" aria-hidden="true">
    <defs>
      <clipPath id="animal-dialog-clip" clipPathUnits="objectBoundingBox">
        <path d="M0.501,0.005 L0.501,0.005 L0.523,0.005 L0.549,0.006 C0.704,0.01,0.796,0.017,0.825,0.027 L0.827,0.028 C0.872,0.045,0.939,0.044,0.978,0.17 C1,0.254,1,0.365,0.99,0.505 L0.988,0.513 C0.979,0.558,0.971,0.598,0.965,0.633 C0.956,0.689,0.979,0.77,0.964,0.865 C0.953,0.928,0.921,0.966,0.869,0.979 C0.821,0.986,0.773,0.992,0.726,0.995 L0.712,0.996 L0.694,0.997 C0.648,1,0.586,1,0.507,1 L0.501,1 L0.464,1 C0.385,1,0.325,0.998,0.283,0.995 C0.234,0.992,0.184,0.987,0.133,0.979 C0.081,0.966,0.05,0.928,0.039,0.865 C0.023,0.77,0.047,0.689,0.037,0.633 C0.031,0.595,0.023,0.552,0.013,0.505 C-0.006,0.365,-0.002,0.254,0.024,0.17 C0.064,0.045,0.13,0.045,0.174,0.028 L0.175,0.028 C0.204,0.017,0.303,0.009,0.474,0.005 L0.501,0.005"/>
      </clipPath>
    </defs>
  </svg>

  <div class="dialog-box">
    <!-- 说话者名牌（搭在对话框左上边缘）-->
    <div class="dialog-speaker">
      <div class="speaker-avatar"><!-- 2 字缩写 --></div>
      <div class="speaker-name">
        <!-- 姓名 -->
        <small><!-- 职位 / 角色描述 --></small>
      </div>
    </div>

    <!-- 对话内容 -->
    <div class="dialog-text">
      <p><!-- 第一段对话，1-2 句。<strong> 高亮关键词，<em> 加重次要词 --></p>
      <p><!-- 可选第二段（更长内容请拆分到下一张幻灯片）--></p>
    </div>

    <!-- 闪烁向下箭头 -->
    <div class="dialog-cursor" aria-hidden="true"></div>
  </div>
</section>
```

**密度上限**：1 说话者牌（2 字头像 + 姓名 ≤6 字 + 角色 ≤10 字） + 1-2 段对话（每段 ≤60 字，总字数 ≤100 字）。超出请拆为多张 dialog 幻灯片，营造"连续对话"的叙事感。

---

## LAYOUT U — Icon Constellation（图标星座 / Capability Map）

**使用场景**：技术栈、能力矩阵、产品模块概览、团队职能图。和 Card Grid（Layout B）的区别：B 是规整 3×2 表格强调"枚举"；U 是中心标题 + 周围 4-8 个"卫星"图标节点，强调"以核心为中心向外辐射"的关系。
**背景**：羊皮纸 + 点纹星空背景。
**搭配**：节点内的 SVG 图标推荐从下方"AC 内置图标库（10 枚）"复用，也可用任意单色 inline SVG。

### CSS

```css
.slide-constellation {
  background: var(--bg-parchment);
  padding: clamp(28px,5vh,60px) clamp(40px,6vw,100px);
  justify-content: center; align-items: center;
}

/* 星空点纹 */
.slide-constellation::before {
  content: ''; position: absolute; inset: 0;
  background-image: radial-gradient(circle, rgba(159,146,125,0.18) 1.2px, transparent 1.4px);
  background-size: 32px 32px;
  pointer-events: none;
}

/* 中央徽章 */
.constellation-center {
  position: relative; z-index: 3;
  background: var(--bg-card);
  border: var(--border-std);
  border-radius: var(--radius-organic);
  padding: clamp(20px,3vh,32px) clamp(28px,4vw,44px);
  box-shadow: var(--shadow-card);
  text-align: center;
  max-width: clamp(280px,38vw,420px);
  opacity: 0; transform: scale(0.85);
  transition: opacity 0.5s var(--ease-ac), transform 0.5s var(--ease-ac);
}
.slide.entered .constellation-center      { opacity: 1; transform: scale(1); transition-delay: 0.05s; }
.slide:not(.entered) .constellation-center { transition-delay: 0s; }

.center-eyebrow {
  font-size: clamp(10px,1.2vw,12px); font-weight: 700;
  color: var(--accent-teal); letter-spacing: 0.14em; text-transform: uppercase;
  margin-bottom: clamp(6px,1vh,10px);
}
.center-title {
  font-size: clamp(22px,3vw,38px); font-weight: 900;
  color: var(--text-primary); line-height: 1.15;
}
.center-caption {
  margin-top: clamp(6px,1vh,12px);
  font-size: clamp(12px,1.4vw,15px); font-weight: 600;
  color: var(--text-body); line-height: 1.45;
}

/* 卫星节点（外层定位 + 内层动画分离）*/
.cnode {
  position: absolute;
  z-index: 2;
}
.cnode-inner {
  display: flex; flex-direction: column; align-items: center;
  gap: clamp(6px,1vh,10px);
  opacity: 0; transform: scale(0.4);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .cnode-inner {
  opacity: 1; transform: scale(1);
  transition-delay: calc(0.25s + var(--i,0) * 0.07s);
}
.slide:not(.entered) .cnode-inner { transition-delay: 0s; }

.cnode-icon {
  width: clamp(54px,7vw,86px); height: clamp(54px,7vw,86px);
  border-radius: 50%;
  background: var(--bg-card);
  border: var(--border-std);
  display: flex; align-items: center; justify-content: center;
  box-shadow: 0 5px 0 0 #bdaea0;
  transition: transform 0.25s var(--ease-ac), box-shadow 0.25s var(--ease-ac);
}
.cnode:hover .cnode-icon {
  transform: translateY(-4px);
  box-shadow: 0 9px 0 0 #bdaea0;
}
.cnode-icon svg { width: 60%; height: 60%; display: block; }

.cnode-label {
  font-size: clamp(11px,1.3vw,14px); font-weight: 700;
  color: var(--text-body);
  background: rgba(255,255,255,0.85);
  padding: 3px 10px; border-radius: 12px;
  letter-spacing: 0.02em; white-space: nowrap;
}

/* 8 个轨道位置 — 中心居中类用 translate */
.cnode-pos-1 { top: 7%;    left: 12%;   }
.cnode-pos-2 { top: 4%;    left: 50%;  transform: translateX(-50%); }
.cnode-pos-3 { top: 7%;    right: 12%;  }
.cnode-pos-4 { top: 48%;   left: 4%;   transform: translateY(-50%); }
.cnode-pos-5 { top: 48%;   right: 4%;  transform: translateY(-50%); }
.cnode-pos-6 { bottom: 7%; left: 12%;   }
.cnode-pos-7 { bottom: 4%; left: 50%;  transform: translateX(-50%); }
.cnode-pos-8 { bottom: 7%; right: 12%;  }

@media (max-width: 900px) {
  .cnode-icon { width: clamp(44px,9vw,64px); height: clamp(44px,9vw,64px); }
  .cnode-label { font-size: clamp(10px,2vw,12px); }
}
```

### HTML 片段

```html
<section class="slide slide-constellation" data-slide="N">
  <!-- 中央徽章 -->
  <div class="constellation-center">
    <div class="center-eyebrow"><!-- 章节小标签 --></div>
    <h2 class="center-title"><!-- 主标题 --></h2>
    <p class="center-caption"><!-- 1 行说明 --></p>
  </div>

  <!-- 4-8 个卫星节点；--i 序号从 0 递增控制入场顺序；cnode-pos-N 控制位置 -->
  <div class="cnode cnode-pos-1">
    <div class="cnode-inner" style="--i:0;">
      <div class="cnode-icon">
        <!-- 内联 SVG 图标。建议从下方"AC 内置图标库"复制（如 icon-chat / icon-camera） -->
      </div>
      <div class="cnode-label"><!-- 标签 ≤8 字 --></div>
    </div>
  </div>

  <div class="cnode cnode-pos-3">
    <div class="cnode-inner" style="--i:1;">
      <div class="cnode-icon"><!-- SVG --></div>
      <div class="cnode-label">…</div>
    </div>
  </div>

  <div class="cnode cnode-pos-4">
    <div class="cnode-inner" style="--i:2;">
      <div class="cnode-icon"><!-- SVG --></div>
      <div class="cnode-label">…</div>
    </div>
  </div>

  <div class="cnode cnode-pos-5">
    <div class="cnode-inner" style="--i:3;">
      <div class="cnode-icon"><!-- SVG --></div>
      <div class="cnode-label">…</div>
    </div>
  </div>

  <div class="cnode cnode-pos-6">
    <div class="cnode-inner" style="--i:4;">
      <div class="cnode-icon"><!-- SVG --></div>
      <div class="cnode-label">…</div>
    </div>
  </div>

  <div class="cnode cnode-pos-8">
    <div class="cnode-inner" style="--i:5;">
      <div class="cnode-icon"><!-- SVG --></div>
      <div class="cnode-label">…</div>
    </div>
  </div>
</section>
```

**密度上限**：1 中央标题（≤12 字） + 可选 1 caption（≤24 字） + 4-8 个卫星节点（每节点 label ≤8 字）。4-6 个节点选用四角 + 四边的子集（推荐用 1/3/4/5/6/8 这 6 个）；7-8 个节点才用满 1-8 全部。`--i` 序号必须从 0 起、按视觉读顺序递增。

---

## LAYOUT S — Tabs Switcher（标签切换 / Tabs）

**使用场景**：多面板同主题对照——"今日/本周/本月"、"方案 A / B / C"、"前端/后端/移动端"、"过去/现在/未来"。和 Comparison（Layout I）的区别：I 是两栏并排展示；S 是同一区域切换不同视角，强调"渐进发现"。
**背景**：羊皮纸；标签栏使用 animal-island-ui Tabs 的标志性 active teal + 摇摆叶子角标。
**交互依赖**：需要在 BASE 的 `<script>` 末尾追加"交互脚本钩子"section 里的 Tabs 片段（见下方）。

### CSS

```css
.slide-tabs {
  background: var(--bg-parchment);
  padding: clamp(28px,4.5vh,52px) clamp(40px,6vw,80px) clamp(40px,6vh,72px);
  justify-content: flex-start;
}

/* 标题 */
.tabs-header {
  margin-bottom: clamp(16px,2.5vh,28px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .tabs-header      { opacity: 1; transform: translateY(0); transition-delay: 0.05s; }
.slide:not(.entered) .tabs-header { transition-delay: 0s; }

.tabs-label {
  font-size: clamp(10px,1.2vw,12px); font-weight: 700;
  color: var(--accent-teal); letter-spacing: 0.12em; text-transform: uppercase;
  margin-bottom: 6px;
}
.tabs-title {
  font-size: clamp(24px,3.5vw,46px); font-weight: 900;
  color: var(--text-primary); line-height: 1.15;
}

/* 标签栏 */
.tabs-bar {
  display: flex; flex-wrap: wrap;
  gap: clamp(8px,1vw,14px);
  padding: clamp(10px,1.5vh,16px) clamp(14px,1.5vw,20px);
  background: rgba(255,255,255,0.6);
  border: var(--border-std);
  border-radius: var(--radius-card);
  margin-bottom: clamp(14px,2.2vh,24px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .tabs-bar      { opacity: 1; transform: translateY(0); transition-delay: 0.15s; }
.slide:not(.entered) .tabs-bar { transition-delay: 0s; }

.tab-btn {
  position: relative;
  display: inline-flex; align-items: center; gap: 8px;
  padding: clamp(8px,1.2vh,12px) clamp(16px,2vw,24px);
  background: transparent; border: none;
  border-radius: var(--radius-pill);
  font-family: var(--font-main);
  font-size: clamp(13px,1.5vw,16px); font-weight: 600;
  color: var(--text-body);
  cursor: pointer;
  transition: all 0.25s var(--ease-ac);
}
.tab-btn:hover { background: rgba(25,200,185,0.12); }
.tab-btn.active {
  background: #0CC0B5;
  color: #FFF9E3;
  font-weight: 700;
  box-shadow: 0 3px 0 0 #099a92;
}

.tab-indicator {
  display: inline-block;
  font-size: 14px; line-height: 1;
  transition: transform 0.25s var(--ease-ac);
}
.tab-btn.active .tab-indicator { transform: scale(1.2); }

/* active 标签右上角摇摆叶子（复刻 Tabs.tabLeaf）*/
.tab-leaf {
  position: absolute;
  top: -8px; right: -6px;
  width: clamp(16px,1.8vw,22px); height: clamp(16px,1.8vw,22px);
  color: var(--green);
  opacity: 0;
  transition: opacity 0.2s var(--ease-ac);
  pointer-events: none;
}
.tab-btn.active .tab-leaf {
  opacity: 1;
  animation: tabLeafWiggle 2s ease-in-out infinite;
}
@keyframes tabLeafWiggle {
  0%,100% { transform: rotate(0deg); }
  25%     { transform: rotate(-10deg); }
  75%     { transform: rotate(10deg); }
}

/* 面板容器：所有面板叠加，只显示 active */
.tabs-panels {
  position: relative;
  flex: 1; min-height: 0;
}
.tab-panel {
  position: absolute; inset: 0;
  opacity: 0; transform: translateY(8px);
  pointer-events: none;
  transition: opacity 0.3s var(--ease-ac), transform 0.3s var(--ease-ac);
}
.tab-panel.active {
  opacity: 1; transform: translateY(0);
  pointer-events: all;
}

/* 单面板内容：标题 + 卡片网格 */
.panel-title {
  font-size: clamp(18px,2.2vw,26px); font-weight: 800;
  color: var(--text-primary);
  margin-bottom: clamp(10px,1.5vh,16px);
}
.panel-grid {
  display: grid; gap: clamp(10px,1.5vw,16px);
  grid-template-columns: repeat(2,1fr);
}
.panel-item {
  display: flex; align-items: flex-start; gap: 12px;
  padding: clamp(12px,1.5vh,18px) clamp(14px,1.6vw,20px);
  background: var(--bg-card);
  border-radius: var(--radius-sm);
  border: var(--border-std);
}
.panel-icon {
  width: clamp(24px,2.5vw,30px); height: clamp(24px,2.5vw,30px);
  border-radius: 8px;
  background: var(--accent-teal);
  display: flex; align-items: center; justify-content: center;
  box-shadow: 0 3px 0 0 var(--accent-teal-a);
  flex-shrink: 0;
}
.panel-item-text {
  font-size: clamp(13px,1.5vw,16px); font-weight: 600;
  color: var(--text-body); line-height: 1.5;
}
.panel-item-text strong { color: var(--text-primary); font-weight: 800; }

@media (max-width: 768px) {
  .panel-grid { grid-template-columns: 1fr; }
}
```

### HTML 片段

```html
<section class="slide slide-tabs" data-slide="N">
  <div class="tabs-header">
    <div class="tabs-label"><!-- 顶部小标签 --></div>
    <h2 class="tabs-title"><!-- 主标题 --></h2>
  </div>

  <!-- 标签栏：2-4 个 .tab-btn；首个加 .active -->
  <div class="tabs-bar">
    <button type="button" class="tab-btn active" data-tab="0">
      <span class="tab-indicator">●</span>
      <span><!-- 标签 1 文本 --></span>
      <svg class="tab-leaf" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true"><path d="M17,8C8,10 5.9,16.17 3.82,21.34L5.71,22L6.66,19.7C7.14,19.87 7.64,20 8,20C19,20 22,3 22,3C21,5 14,5.25 9,6.25C4,7.25 2,11.5 2,13.5C2,15.5 3.75,17.25 3.75,17.25C7,8 17,8 17,8Z"/></svg>
    </button>
    <button type="button" class="tab-btn" data-tab="1">
      <span class="tab-indicator">○</span>
      <span><!-- 标签 2 --></span>
      <svg class="tab-leaf" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true"><path d="M17,8C8,10 5.9,16.17 3.82,21.34L5.71,22L6.66,19.7C7.14,19.87 7.64,20 8,20C19,20 22,3 22,3C21,5 14,5.25 9,6.25C4,7.25 2,11.5 2,13.5C2,15.5 3.75,17.25 3.75,17.25C7,8 17,8 17,8Z"/></svg>
    </button>
    <button type="button" class="tab-btn" data-tab="2">
      <span class="tab-indicator">○</span>
      <span><!-- 标签 3 --></span>
      <svg class="tab-leaf" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true"><path d="M17,8C8,10 5.9,16.17 3.82,21.34L5.71,22L6.66,19.7C7.14,19.87 7.64,20 8,20C19,20 22,3 22,3C21,5 14,5.25 9,6.25C4,7.25 2,11.5 2,13.5C2,15.5 3.75,17.25 3.75,17.25C7,8 17,8 17,8Z"/></svg>
    </button>
  </div>

  <!-- 面板容器：每个 .tab-panel 与上方按钮顺序对应；首个加 .active -->
  <div class="tabs-panels">
    <div class="tab-panel active">
      <h3 class="panel-title"><!-- 面板 1 副标题 --></h3>
      <div class="panel-grid">
        <!-- 2-4 个 panel-item -->
        <div class="panel-item">
          <div class="panel-icon">
            <svg width="14" height="14" viewBox="0 0 13 13" fill="none" stroke="#fff" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"><polyline points="2 7 5 10 11 3"/></svg>
          </div>
          <div class="panel-item-text">
            <!-- 要点文本。<strong> 高亮关键词 -->
          </div>
        </div>
        <!-- 重复至多 4 个 -->
      </div>
    </div>

    <div class="tab-panel">
      <h3 class="panel-title"><!-- 面板 2 副标题 --></h3>
      <div class="panel-grid"><!-- 2-4 个 panel-item --></div>
    </div>

    <div class="tab-panel">
      <h3 class="panel-title"><!-- 面板 3 副标题 --></h3>
      <div class="panel-grid"><!-- 2-4 个 panel-item --></div>
    </div>
  </div>
</section>
```

**密度上限**：1 标题 + 2-4 个 tab + 每个 panel 内 1 副标题 + 2-4 个 panel-item（每 item ≤40 字）。每个 panel 占据相同空间，所以所有 panel 的内容量必须均衡——若某 panel 远超其他，请拆为多张 Tabs 幻灯片。所有 tab 必须有匹配的 panel；首个 tab/panel 必须加 `.active`。

---

## LAYOUT W — Collapse Stack（渐次展开 / Accordion）

**使用场景**：渐进披露内容、步骤详解、复盘要点、深度问题清单。和 FAQ（Layout P）的区别：P 一次性展示所有 Q+A；W 默认折叠，点击逐项展开，强调"逐步揭示"的节奏，适合内容较多但读者需要节奏感的场景。
**背景**：羊皮纸；用 animal-island-ui Collapse 组件的 `grid-template-rows: 0fr → 1fr` 纯 CSS 展开动画，无需 JS 测量高度。
**交互依赖**：需要在 BASE 的 `<script>` 末尾追加"交互脚本钩子"section 里的 Collapse 片段（见下方）。

### CSS

```css
.slide-collapse {
  background: var(--bg-parchment);
  padding: clamp(28px,4.5vh,52px) clamp(40px,7vw,140px) clamp(40px,6vh,72px);
  justify-content: flex-start;
}

.collapse-header {
  margin-bottom: clamp(18px,3vh,32px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .collapse-header      { opacity: 1; transform: translateY(0); transition-delay: 0.05s; }
.slide:not(.entered) .collapse-header { transition-delay: 0s; }

.collapse-label { font-size: clamp(10px,1.2vw,12px); font-weight: 700; color: var(--accent-teal); letter-spacing: 0.12em; text-transform: uppercase; margin-bottom: 6px; }
.collapse-title { font-size: clamp(24px,3.5vw,46px); font-weight: 900; color: var(--text-primary); line-height: 1.15; }

.collapse-list {
  display: flex; flex-direction: column;
  gap: clamp(10px,1.5vh,16px);
}

.collapse-item {
  background: var(--bg-card);
  border: var(--border-std);
  border-radius: var(--radius-card);
  overflow: hidden;
  opacity: 0; transform: translateY(14px);
  transition:
    opacity 0.4s var(--ease-ac),
    transform 0.4s var(--ease-ac),
    border-color 0.25s var(--ease-ac);
}
.slide.entered .collapse-item {
  opacity: 1; transform: translateY(0);
  transition-delay: calc(0.15s + var(--i,0) * 0.08s);
}
.slide:not(.entered) .collapse-item { transition-delay: 0s; }

.collapse-item.expanded { border-color: var(--accent-teal); }

/* 题目栏（可点击）*/
.collapse-q {
  display: flex; align-items: center;
  gap: clamp(12px,1.5vw,16px);
  width: 100%;
  padding: clamp(14px,2vh,20px) clamp(18px,2.5vw,28px);
  background: transparent; border: none;
  text-align: left; cursor: pointer;
  font-family: var(--font-main);
}

/* +/− 圆形图标 — JS 切换 .expanded 时同时切换文本内容 */
.collapse-q-icon {
  width: clamp(26px,2.8vw,32px); height: clamp(26px,2.8vw,32px);
  border-radius: 50%;
  background: var(--accent-teal); color: #fff;
  display: flex; align-items: center; justify-content: center;
  font-size: clamp(16px,1.8vw,20px); font-weight: 900; line-height: 1;
  flex-shrink: 0;
  box-shadow: 0 2px 4px rgba(25,200,185,0.3);
  transition:
    background-color 0.25s var(--ease-ac),
    transform 0.3s var(--ease-ac);
}
.collapse-item.expanded .collapse-q-icon {
  background: var(--accent-teal-a);
  transform: rotate(180deg);
}

.collapse-q-text {
  flex: 1;
  font-size: clamp(14px,1.7vw,18px); font-weight: 600;
  color: var(--text-primary); line-height: 1.4;
}

/* 右侧叶子装饰 — 展开时旋转 45deg */
.collapse-leaf {
  color: var(--green); opacity: 0.45;
  flex-shrink: 0;
  transition: opacity 0.25s var(--ease-ac), transform 0.3s var(--ease-ac);
}
.collapse-item.expanded .collapse-leaf {
  opacity: 1; transform: rotate(45deg);
}

/* 答案包裹：grid-template-rows 0fr → 1fr 动画 */
.collapse-a {
  display: grid; grid-template-rows: 0fr;
  transition: grid-template-rows 0.32s var(--ease-ac);
}
.collapse-item.expanded .collapse-a { grid-template-rows: 1fr; }

.collapse-a-inner {
  overflow: hidden;
  padding: 0 clamp(18px,2.5vw,28px);
  font-size: clamp(13px,1.5vw,16px); font-weight: 500;
  color: var(--text-body); line-height: 1.7;
  transition: padding 0.25s var(--ease-ac);
}
.collapse-item.expanded .collapse-a-inner {
  padding-bottom: clamp(16px,2.2vh,24px);
}
.collapse-a-inner p + p { margin-top: clamp(6px,1vh,10px); }
.collapse-a-inner strong { color: var(--text-primary); font-weight: 700; }
.collapse-a-inner ul {
  margin: clamp(6px,1vh,10px) 0;
  padding-left: clamp(18px,2.5vw,28px);
}
.collapse-a-inner li { margin-bottom: 4px; }
```

### HTML 片段

```html
<section class="slide slide-collapse" data-slide="N">
  <div class="collapse-header">
    <div class="collapse-label"><!-- 顶部小标签 --></div>
    <h2 class="collapse-title"><!-- 主标题 --></h2>
  </div>

  <div class="collapse-list">
    <!-- 每个 collapse-item；--i 控制入场顺序（从 0 起）；首项可选加 .expanded 让其默认展开 -->
    <div class="collapse-item expanded" style="--i:0;">
      <button class="collapse-q" type="button" aria-expanded="true">
        <span class="collapse-q-icon">−</span>
        <span class="collapse-q-text"><!-- 问题 / 步骤标题 --></span>
        <svg class="collapse-leaf" viewBox="0 0 24 24" width="20" height="20" aria-hidden="true">
          <path fill="currentColor" d="M17,8C8,10 5.9,16.17 3.82,21.34L5.71,22L6.66,19.7C7.14,19.87 7.64,20 8,20C19,20 22,3 22,3C21,5 14,5.25 9,6.25C4,7.25 2,11.5 2,13.5C2,15.5 3.75,17.25 3.75,17.25C7,8 17,8 17,8Z"/>
        </svg>
      </button>
      <div class="collapse-a">
        <div class="collapse-a-inner">
          <p><!-- 答案 / 详情段落 1。<strong> 高亮关键词 --></p>
          <ul>
            <li><!-- 子要点 --></li>
            <li><!-- 子要点 --></li>
          </ul>
        </div>
      </div>
    </div>

    <div class="collapse-item" style="--i:1;">
      <button class="collapse-q" type="button" aria-expanded="false">
        <span class="collapse-q-icon">+</span>
        <span class="collapse-q-text">…</span>
        <svg class="collapse-leaf" viewBox="0 0 24 24" width="20" height="20" aria-hidden="true">
          <path fill="currentColor" d="M17,8C8,10 5.9,16.17 3.82,21.34L5.71,22L6.66,19.7C7.14,19.87 7.64,20 8,20C19,20 22,3 22,3C21,5 14,5.25 9,6.25C4,7.25 2,11.5 2,13.5C2,15.5 3.75,17.25 3.75,17.25C7,8 17,8 17,8Z"/>
        </svg>
      </button>
      <div class="collapse-a">
        <div class="collapse-a-inner">
          <p>…</p>
        </div>
      </div>
    </div>

    <!-- 重复至多 5 个 collapse-item，每个 --i 顺序递增 -->
  </div>
</section>
```

**密度上限**：1 标题 + 3-5 个 collapse-item（每 item：问题 ≤30 字 + 答案 1-2 段或 ≤4 条 li 列表，单 item 答案展开后视觉高度 ≤30vh）。首项推荐加 `.expanded` 让用户进入时已能看到一份示例展开内容。

---

## LAYOUT T — Settings Panel（配置面板 / Options Mockup）

**使用场景**：功能开关清单、特性对比、产品规格表、"选择你的路线"、人格 / 角色偏好。和 Card Grid（Layout B）的区别：B 是平铺枚举；T 是**带控件状态**的可交互拟态，强调"这些选项可以被配置"，把"参数 / 决策点"具象化。
**背景**：羊皮纸；中心放置一张"设置卡片"，内含 3-6 条 settings-row，每行 = 图标 + 标签 + 描述 + 控件。
**复刻组件**：Switch、Checkbox、Select 的 AC 视觉精确还原（颜色、阴影、动画来自仓库源样式）。
**交互依赖**：需要在 BASE 的 `<script>` 末尾追加"交互脚本钩子"section 里的 Settings 片段（Switch/Checkbox/Segmented 都能点击切换）。

### CSS

```css
.slide-settings {
  background: var(--bg-parchment);
  padding: clamp(32px,5vh,60px) clamp(40px,6vw,80px);
  justify-content: center; align-items: center;
}

.settings-header {
  text-align: center;
  margin-bottom: clamp(18px,3vh,28px);
  opacity: 0; transform: translateY(12px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .settings-header      { opacity: 1; transform: translateY(0); transition-delay: 0.05s; }
.slide:not(.entered) .settings-header { transition-delay: 0s; }

.settings-label   { font-size: clamp(10px,1.2vw,12px); font-weight: 700; color: var(--accent-teal); letter-spacing: 0.12em; text-transform: uppercase; margin-bottom: 6px; }
.settings-title   { font-size: clamp(24px,3.5vw,42px); font-weight: 900; color: var(--text-primary); line-height: 1.15; }
.settings-caption { margin-top: 6px; font-size: clamp(12px,1.4vw,15px); font-weight: 500; color: var(--text-body); line-height: 1.45; }

/* 设置卡片 */
.settings-card {
  width: min(720px, 90%);
  background: var(--bg-card);
  border: var(--border-std);
  border-radius: var(--radius-card);
  padding: clamp(16px,2.5vh,24px) clamp(20px,3vw,32px);
  box-shadow: var(--shadow-card);
  opacity: 0; transform: translateY(16px);
  transition: opacity 0.5s var(--ease-ac), transform 0.5s var(--ease-ac);
}
.slide.entered .settings-card      { opacity: 1; transform: translateY(0); transition-delay: 0.15s; }
.slide:not(.entered) .settings-card { transition-delay: 0s; }

/* 每行设置 */
.settings-row {
  display: flex; align-items: center;
  gap: clamp(12px,1.5vw,18px);
  padding: clamp(10px,1.4vh,16px) 0;
  border-bottom: 2px dashed rgba(159,146,125,0.35);
  opacity: 0; transform: translateY(8px);
  transition: opacity 0.4s var(--ease-ac), transform 0.4s var(--ease-ac);
}
.settings-row:last-child { border-bottom: none; }
.slide.entered .settings-row {
  opacity: 1; transform: translateY(0);
  transition-delay: calc(0.25s + var(--i,0) * 0.08s);
}
.slide:not(.entered) .settings-row { transition-delay: 0s; }

.row-icon {
  width: clamp(36px,4vw,46px); height: clamp(36px,4vw,46px);
  border-radius: 12px;
  background: var(--card-blue);
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
  box-shadow: 0 3px 0 0 rgba(0,0,0,0.1);
}

.row-text { flex: 1; min-width: 0; display: flex; flex-direction: column; gap: 2px; }
.row-label { font-size: clamp(14px,1.6vw,17px); font-weight: 700; color: var(--text-primary); line-height: 1.3; }
.row-desc  { font-size: clamp(11px,1.2vw,13px); font-weight: 500; color: var(--text-muted); line-height: 1.4; }

/* ── AC Switch（拨片）── */
.ac-switch {
  position: relative;
  display: inline-flex; align-items: center;
  width: clamp(46px,5vw,56px); height: clamp(24px,2.6vh,28px);
  padding: 0;
  background: #d4c9b4;
  border: 2.5px solid #c4b89e;
  border-radius: 50px;
  cursor: pointer; flex-shrink: 0;
  box-shadow: inset 0 2px 4px rgba(114,93,66,0.15);
  transition: all 0.25s var(--ease-ac);
}
.ac-switch:hover         { border-color: #a89878; }
.ac-switch.on            { background: #86d67a; border-color: #6fba2c; box-shadow: inset 0 2px 4px rgba(90,158,30,0.2); }
.ac-switch.on:hover      { border-color: #5a9e1e; background: #7ccc70; }
.ac-switch-handle {
  position: absolute; top: 50%; left: 1px;
  transform: translateY(-50%);
  width: clamp(18px,2vw,21px); height: clamp(18px,2vw,21px);
  background: rgb(247, 243, 223);
  border: 2.5px solid #c4b89e;
  border-radius: 50%;
  transition: all 0.25s var(--ease-ac);
}
.ac-switch.on .ac-switch-handle { left: calc(100% - clamp(20px,2.2vw,24px)); border-color: #6fba2c; }

/* ── AC Checkbox（勾选框组）── */
.ac-checkboxes { display: flex; gap: clamp(10px,1.5vw,16px); flex-wrap: wrap; }
.ac-checkbox {
  display: inline-flex; align-items: center;
  gap: 8px; cursor: pointer; user-select: none;
}
.ac-checkbox-box {
  width: clamp(20px,2.2vw,24px); height: clamp(20px,2.2vw,24px);
  background: rgb(247, 243, 223);
  border: 2px solid #c4b89e;
  border-radius: 6px;
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
  transition: all 0.2s var(--ease-ac);
}
.ac-checkbox.on .ac-checkbox-box {
  background: var(--accent-teal);
  border-color: var(--accent-teal-a);
}
.ac-checkbox-mark {
  width: 12px; height: 12px; color: #fff;
  opacity: 0; transform: scale(0.4);
  transition: opacity 0.2s var(--ease-ac), transform 0.2s var(--ease-ac);
}
.ac-checkbox.on .ac-checkbox-mark { opacity: 1; transform: scale(1); }
.ac-checkbox-label { font-size: clamp(12px,1.3vw,14px); font-weight: 600; color: var(--text-body); }

/* ── AC Segmented（3 段切换）── */
.ac-segmented {
  display: inline-flex;
  padding: 3px;
  background: rgba(159,146,125,0.15);
  border-radius: 50px;
  gap: 2px;
}
.ac-segmented-btn {
  padding: clamp(5px,0.8vh,7px) clamp(10px,1.4vw,16px);
  background: transparent; border: none; border-radius: 50px;
  font-family: var(--font-main);
  font-size: clamp(11px,1.2vw,13px); font-weight: 700;
  color: var(--text-muted);
  cursor: pointer;
  transition: all 0.2s var(--ease-ac);
}
.ac-segmented-btn.on {
  background: var(--accent-yellow);
  color: var(--text-primary);
  box-shadow: 0 2px 0 0 var(--accent-yellow-d);
}

/* ── AC Value Chip（只读"当前值"展示，模拟 Select trigger）── */
.ac-value-chip {
  display: inline-flex; align-items: center; gap: 8px;
  padding: clamp(6px,0.9vh,9px) clamp(12px,1.5vw,18px);
  background: #fff;
  border: 2px solid #e8dcc8;
  border-radius: 12px;
  font-size: clamp(13px,1.5vw,15px); font-weight: 700;
  color: var(--text-body);
  flex-shrink: 0;
}
.ac-value-chip-dot { width: 8px; height: 8px; border-radius: 50%; background: var(--accent-teal); }
```

### HTML 片段

```html
<section class="slide slide-settings" data-slide="N">
  <div class="settings-header">
    <div class="settings-label"><!-- 顶部小标签 --></div>
    <h2 class="settings-title"><!-- 主标题 --></h2>
    <p class="settings-caption"><!-- 1 行说明 --></p>
  </div>

  <div class="settings-card">
    <!-- ROW 1：Switch（开关）-->
    <div class="settings-row" style="--i:0;">
      <div class="row-icon" style="background: var(--card-teal);">
        <!-- 内联 SVG icon（22×22，stroke="#fff"），可用"AC 内置图标库"或"常用内联 SVG 图标库" -->
      </div>
      <div class="row-text">
        <div class="row-label"><!-- 开关项标题 --></div>
        <div class="row-desc"><!-- 一行说明 --></div>
      </div>
      <button type="button" class="ac-switch on" aria-pressed="true">
        <span class="ac-switch-handle"></span>
      </button>
    </div>

    <!-- ROW 2：Checkbox 多选 -->
    <div class="settings-row" style="--i:1;">
      <div class="row-icon" style="background: var(--card-yellow);"><!-- icon --></div>
      <div class="row-text">
        <div class="row-label"><!-- 多选标题 --></div>
        <div class="row-desc"><!-- 描述 --></div>
      </div>
      <div class="ac-checkboxes">
        <div class="ac-checkbox on">
          <div class="ac-checkbox-box">
            <svg class="ac-checkbox-mark" viewBox="0 0 13 13" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="2 7 5 10 11 3"/></svg>
          </div>
          <span class="ac-checkbox-label">选项 A</span>
        </div>
        <div class="ac-checkbox">
          <div class="ac-checkbox-box">
            <svg class="ac-checkbox-mark" viewBox="0 0 13 13" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="2 7 5 10 11 3"/></svg>
          </div>
          <span class="ac-checkbox-label">选项 B</span>
        </div>
      </div>
    </div>

    <!-- ROW 3：Segmented（单选 3 段）-->
    <div class="settings-row" style="--i:2;">
      <div class="row-icon" style="background: var(--card-pink);"><!-- icon --></div>
      <div class="row-text">
        <div class="row-label"><!-- 单选 segmented 标题 --></div>
        <div class="row-desc"><!-- 描述 --></div>
      </div>
      <div class="ac-segmented">
        <button type="button" class="ac-segmented-btn">节 能</button>
        <button type="button" class="ac-segmented-btn on">标 准</button>
        <button type="button" class="ac-segmented-btn">性 能</button>
      </div>
    </div>

    <!-- ROW 4：Value Chip（只读"当前值"展示）-->
    <div class="settings-row" style="--i:3;">
      <div class="row-icon" style="background: var(--card-orange);"><!-- icon --></div>
      <div class="row-text">
        <div class="row-label"><!-- 只读项标题 --></div>
        <div class="row-desc"><!-- 描述 --></div>
      </div>
      <div class="ac-value-chip">
        <span class="ac-value-chip-dot"></span>
        <span><!-- 当前值文字 --></span>
      </div>
    </div>
  </div>
</section>
```

**密度上限**：1 标题 + 可选 1 caption + **3-6 条 settings-row**。每行控件**只能选一种**（Switch / Checkboxes / Segmented / Value chip）；同一卡片内推荐混用 2-4 种控件类型，给读者"这是真的可配置界面"的感觉。`--i` 序号从 0 顺序递增。

---

## 氛围工具（Ambient Tools — 可叠加在任意布局上）

下面 3 个不是新布局，是可以**任意叠加**到现有幻灯片上的装饰组件。

### 工具 1 — Time HUD（角落实时时钟）

**用途**：发布会现场感、Live 演示、午间汇报。固定在右上角，永远显示真实当前时间。
**视觉**：羊皮纸渐变底 + 浅棕双线边框，左侧 weekday + month-day，右侧大字时钟（冒号闪烁）。
**对应组件**：精确复刻 `time.module.less`。

```css
/* 追加到 BASE <style> 末尾 */
.ambient-time {
  position: fixed;
  top: clamp(14px,2.5vh,28px);
  right: clamp(14px,2vw,28px);
  display: inline-flex; gap: clamp(10px,1.4vw,20px);
  align-items: center;
  padding: clamp(8px,1.2vh,12px) clamp(14px,2vw,24px);
  background: linear-gradient(180deg, #fff 0%, #f8f8f0 100%);
  border: 3px solid #d4cfc3;
  border-radius: 14px;
  z-index: 90;
  pointer-events: none;
}
.ambient-time-date {
  display: flex; flex-direction: column; align-items: center;
  padding-right: clamp(10px,1.4vw,20px);
  border-right: 3px solid rgba(159,146,125,0.35);
}
.ambient-time-weekday {
  color: #6fba2c; font-weight: 900;
  font-size: clamp(10px,1.2vw,13px);
  letter-spacing: 1.5px; text-transform: uppercase;
}
.ambient-time-monthday {
  color: #8b7355; font-weight: 800;
  font-size: clamp(15px,1.8vw,21px);
}
.ambient-time-clock {
  display: flex; align-items: center;
  color: #8b7355; font-weight: 900;
  font-size: clamp(28px,3.5vw,42px);
  letter-spacing: 2px; line-height: 1;
}
.ambient-time-colon {
  position: relative; top: -0.08em; margin: 0 1px;
  animation: ambientBlink 1s step-end infinite;
}
@keyframes ambientBlink { 50% { opacity: 0; } }
```

```html
<!-- 紧贴在 <div class="deck"> 之后（与导航箭头同级），整份 deck 共享一个 HUD -->
<div class="ambient-time" id="ambient-time" aria-hidden="true">
  <div class="ambient-time-date">
    <span class="ambient-time-weekday">SAT</span>
    <span class="ambient-time-monthday">May 17</span>
  </div>
  <div class="ambient-time-clock">
    <span class="ambient-time-hh">12</span><span class="ambient-time-colon">:</span><span class="ambient-time-mm">34</span>
  </div>
</div>
```

```javascript
// 追加到 BASE <script> 末尾
(function(){
  const el = document.getElementById('ambient-time');
  if (!el) return;
  const wkEl = el.querySelector('.ambient-time-weekday');
  const mdEl = el.querySelector('.ambient-time-monthday');
  const hhEl = el.querySelector('.ambient-time-hh');
  const mmEl = el.querySelector('.ambient-time-mm');
  const days   = ['SUN','MON','TUE','WED','THU','FRI','SAT'];
  const months = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'];
  function tick(){
    const d = new Date();
    wkEl.textContent = days[d.getDay()];
    mdEl.textContent = months[d.getMonth()] + ' ' + d.getDate();
    hhEl.textContent = String(d.getHours()).padStart(2,'0');
    mmEl.textContent = String(d.getMinutes()).padStart(2,'0');
  }
  tick(); setInterval(tick, 1000);
})();
```

### 工具 2 — Game Cursor（AC 手指光标）

**用途**：把整份演示变成"游戏画面"——尤其搭配 Layout R（对话框）/ Layout Q（岛屿场景）效果拔群。
**视觉**：黄色填充 + 棕色描边的手指指针光标，光标热点 (4,0)（左上角偏内一点）。
**对应组件**：复刻 Cursor.module.less，把图片路径换成内联 data-URI SVG。

```css
/* 追加到 BASE <style> 末尾 */
.deck.with-ac-cursor,
.deck.with-ac-cursor * {
  cursor: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="28" height="28" viewBox="0 0 28 28"><path d="M6 2 L6 19 L10 15 L13 22 L16.5 20.5 L13.5 14 L19 14 Z" fill="%23ffcc00" stroke="%23725d42" stroke-width="1.5" stroke-linejoin="round" stroke-linecap="round"/></svg>') 4 0, auto !important;
}
```

**开启方式**：在 `<div class="deck">` 上加 `with-ac-cursor` 类即可：

```html
<div class="deck with-ac-cursor" id="deck">
  ...
</div>
```

### 工具 3 — Divider 花纹（5 种横向装饰条）

**用途**：在 Bullets / Quote / FAQ 等内容型布局里**章节之间**做视觉分隔。比纯线条更有 AC 手作感。
**视觉**：12px 高的横向装饰带；4 种圆点花纹（brown / teal / white / yellow）+ 1 种波浪（wave-yellow）。
**对应组件**：复刻 Divider.module.less 的 5 个 type，把图片资源改成 CSS gradient / data-URI SVG。

```css
/* 追加到 BASE <style> 末尾 */
.ac-divider {
  width: 100%; height: 12px;
  margin: clamp(10px,1.5vh,20px) 0;
  background-position: center;
  background-repeat: repeat-x;
}
.ac-divider-brown   { background-image: radial-gradient(circle, #794f27 1.8px, transparent 2px); background-size: 14px 12px; }
.ac-divider-teal    { background-image: radial-gradient(circle, #19c8b9 1.8px, transparent 2px); background-size: 14px 12px; }
.ac-divider-white   { background-image: radial-gradient(circle, #ffffff 1.8px, transparent 2px); background-size: 14px 12px; }
.ac-divider-yellow  { background-image: radial-gradient(circle, #ffcc00 1.8px, transparent 2px); background-size: 14px 12px; }
.ac-divider-wave-yellow {
  background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 40 12" preserveAspectRatio="none"><path d="M0 6 Q10 0 20 6 T40 6" stroke="%23ffcc00" stroke-width="3" fill="none" stroke-linecap="round"/></svg>');
  background-size: 40px 12px;
}
```

```html
<!-- 在内容块之间插入 -->
<div class="ac-divider ac-divider-brown" aria-hidden="true"></div>
<div class="ac-divider ac-divider-teal" aria-hidden="true"></div>
<div class="ac-divider ac-divider-white" aria-hidden="true"></div>
<div class="ac-divider ac-divider-yellow" aria-hidden="true"></div>
<div class="ac-divider ac-divider-wave-yellow" aria-hidden="true"></div>
```

> 默认 `brown` 用于羊皮纸底；`white` 专门用于深色背景（如 Cover 的绿底）；`wave-yellow` 是"庆祝/章节"语义最强的一款，节省着用。

---

## 交互脚本钩子（Layout S / W 必需）

Layout S 和 Layout W 需要这两段 JS。把它们**追加到 BASE 的 `<script>` 块末尾**（即 `enter(slides[0]);` 和 `document.addEventListener('keydown', ...)` 之间或之后均可），整份输出依然是单 HTML、零外部依赖。

```javascript
// ── LAYOUT S: 标签切换 ─────────────────────────
document.querySelectorAll('.slide-tabs').forEach(slide => {
  const btns   = slide.querySelectorAll('.tab-btn');
  const panels = slide.querySelectorAll('.tab-panel');
  btns.forEach((btn, idx) => {
    btn.addEventListener('click', () => {
      btns.forEach((b, i) => {
        b.classList.toggle('active', i === idx);
        const ind = b.querySelector('.tab-indicator');
        if (ind) ind.textContent = (i === idx) ? '●' : '○';
      });
      panels.forEach((p, i) => p.classList.toggle('active', i === idx));
    });
  });
});

// ── LAYOUT W: 折叠展开 ─────────────────────────
document.querySelectorAll('.collapse-q').forEach(btn => {
  btn.addEventListener('click', () => {
    const item = btn.closest('.collapse-item');
    const isExpanded = item.classList.toggle('expanded');
    const icon = btn.querySelector('.collapse-q-icon');
    if (icon) icon.textContent = isExpanded ? '−' : '+';
    btn.setAttribute('aria-expanded', isExpanded ? 'true' : 'false');
  });
});

// ── LAYOUT T: 配置控件（Switch / Checkbox / Segmented）─
document.querySelectorAll('.ac-switch').forEach(sw => {
  sw.addEventListener('click', () => {
    const on = sw.classList.toggle('on');
    sw.setAttribute('aria-pressed', on ? 'true' : 'false');
  });
});
document.querySelectorAll('.ac-checkbox').forEach(cb => {
  cb.addEventListener('click', () => cb.classList.toggle('on'));
});
document.querySelectorAll('.ac-segmented').forEach(seg => {
  seg.querySelectorAll('.ac-segmented-btn').forEach(btn => {
    btn.addEventListener('click', () => {
      seg.querySelectorAll('.ac-segmented-btn').forEach(b => b.classList.remove('on'));
      btn.classList.add('on');
    });
  });
});
```

> 这几段在没有相应布局的演示文稿中也无害（`querySelectorAll` 命中 0 个元素时直接返回）。可以无脑加进所有输出，省去判断。

---

## AC 内置图标库（10 枚 / 源自 animal-island-ui）

**何时使用**：Layout U（图标星座）的节点图标、Card Grid（Layout B）卡片图标、NookPhone Showcase（Layout O）九宫格。这 10 个图标是 animal-island-ui 官方 `ICON_LIST`（NookPhone 应用图标），自带 AC 多色填充，无需 `stroke`。

**通用使用方式**：直接把对应 `<svg>` 嵌进你的容器即可（推荐外层尺寸用 `width: clamp(...)` 自动适配；svg 自身 `viewBox` 已设好不要改）。

```html
<!-- icon-chat（聊天气泡，浅米底 + 三个深色点 + 粉色挂坠）-->
<svg viewBox="0 0 80 67" fill="none" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" clip-rule="evenodd" d="M23.6992 9.64355C12.1012 9.64355 2.69922 19.0456 2.69922 30.6436V38.4145C2.69922 50.0125 12.1012 59.4145 23.6992 59.4145H34.8603L40.1597 66.2754C40.56 66.7937 41.3422 66.7937 41.7425 66.2754L47.042 59.4145H58.203C69.801 59.4145 79.203 50.0124 79.203 38.4145V30.6436C79.203 19.0456 69.801 9.64355 58.203 9.64355H23.6992Z" fill="#F5F1E6"/><circle cx="20.8502" cy="35.8375" r="5.78961" fill="#5E483B"/><circle cx="40.4293" cy="35.8375" r="5.78961" fill="#5E483B"/><circle cx="60.0084" cy="35.8375" r="5.78961" fill="#5E483B"/><rect width="33.5693" height="19.2872" rx="9" fill="#F583A4"/></svg>

<!-- icon-camera（相机：深棕机身 + 米白镜头 + 紫色闪光）-->
<svg viewBox="0 0 85 66" fill="none" xmlns="http://www.w3.org/2000/svg"><rect x="5.94434" y="4.92773" width="15.5386" height="16.1112" rx="1.78001" fill="#FF66AD"/><path fill-rule="evenodd" clip-rule="evenodd" d="M30.585 0H54.7227C56.8954 0.0509226 61.251 1.72121 61.2917 7.99496C61.2917 10.9719 62.9303 12.1182 64.1666 12.4312H70.5935C78.4581 12.4312 84.8336 18.8067 84.8336 26.6712V51.0976C84.8336 58.9622 78.4581 65.3377 70.5935 65.3377H14.2401C6.37551 65.3377 0 58.9622 0 51.0976V26.6712C0 18.8067 6.37549 12.4312 14.2401 12.4312H21.3539C22.5467 12.1169 24.1915 11.0856 24.1915 8.63043C24.1915 4.37225 26.5803 0 30.585 0Z" fill="#4C3C33"/><path d="M47.2051 7.81382H38.5511C36.0335 8.96867 36.4298 12.6061 37.9934 12.6061H48.3218C50.0712 10.9311 48.3218 7.69332 47.2051 7.81382Z" fill="#F9F6E5"/><ellipse cx="42.9035" cy="39.4683" rx="19.7658" ry="19.7652" fill="#F9F6E5"/><path fill-rule="evenodd" clip-rule="evenodd" d="M57.5033 39.468C57.5033 47.5323 50.9659 54.0697 42.9016 54.0697C34.8372 54.0697 28.2998 47.5323 28.2998 39.468C28.2998 31.4036 34.8372 24.8662 42.9016 24.8662C50.9659 24.8662 57.5033 31.4036 57.5033 39.468ZM53.8936 40.9628C53.8936 42.2078 52.8844 43.2171 51.6394 43.2171C50.3945 43.2171 49.3852 42.2078 49.3852 40.9628C49.3852 39.7179 50.3945 38.7086 51.6394 38.7086C52.8844 38.7086 53.8936 39.7179 53.8936 40.9628ZM43.9772 28.6918C42.996 28.6022 41.2035 28.6918 41.0334 30.4128C40.8416 32.3543 42.5052 32.8329 43.9772 32.8866C44.9658 32.9228 47.5574 33.3182 49.1556 35.9975C49.4861 36.571 50.5237 37.5062 52.0306 36.6586C53.5375 35.811 52.4715 33.7908 51.7502 32.8866C50.94 31.6534 48.251 29.0878 43.9772 28.6918Z" fill="#9364DE"/></svg>

<!-- icon-design（调色板 + 画笔，AC 设计画板）-->
<svg viewBox="0 0 98 72" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M0.335777 10.1492C4.89272 4.90487 15.8067 0.905744 20.33 0C22.2521 5.66649 23.6648 7.58058 31.4973 8.31653C39.3299 9.05248 42.7776 1.90327 42.7776 0.0974021C46.7848 1.07591 49.301 2.84653 56.5233 6.71395C62.3011 9.80789 62.955 12.1036 62.5598 12.8647C59.1326 19.2015 54.8059 25.4431 52.631 25.4431C51.1506 25.4431 49.4913 24.2734 49.0295 22.7651C48.5678 21.2568 46.998 21.0413 47.7675 23.2576C48.537 25.4739 51.2217 42.2647 51.1609 42.3863C51.1 42.508 40.1516 45.4883 31.8187 45.8533C23.4858 46.2182 11.9884 42.9227 10.9875 39.6866C9.98669 36.4505 13.2561 26.1083 14.4572 23.039C15.418 20.5836 14.0568 21.7046 13.2561 22.572C12.3154 23.6242 10.0697 25.5898 8.61345 25.035C7.15721 24.4803 2.36158 16.8418 0.145802 13.092C-0.00407374 12.3316 -0.155879 11.1351 0.335777 10.1492Z" fill="#F7F3E1"/><circle cx="31.2875" cy="15.763" r="3.88316" fill="#59433A"/><circle cx="31.2875" cy="25.8568" r="3.88316" fill="#59433A"/><path fill-rule="evenodd" clip-rule="evenodd" d="M72.9897 8.06671L43.1696 38.9087L43.1757 38.9148C42.5864 39.3622 41.205 40.9237 40.3256 43.6513C39.4422 46.3915 36.0168 58.2743 34.4145 63.8732C33.7432 66.3763 33.9857 71.3165 40.3256 71.053C46.4057 69.5775 56.89 66.0908 61.3721 64.5319C62.628 64.0928 65.3476 62.8852 66.1791 61.5678C66.1756 61.5644 66.1721 61.561 66.1685 61.5575L95.882 30.8258C98.7075 27.9035 98.6611 23.2113 95.7784 20.3454L83.3251 7.96458C80.4425 5.0987 75.8151 5.14442 72.9897 8.06671Z" fill="#59433A"/><path d="M42.4894 39.7568C40.4231 41.1563 37.7123 51.2246 36.6152 56.0838C38.3518 57.788 43.3351 62.6673 49.3763 68.5511C59.1201 66.2132 64.0929 63.486 65.3614 62.4147C58.3623 55.5701 43.9892 41.4562 42.4894 39.7568Z" fill="#FFCF4F"/></svg>

<!-- icon-shopping（米袋 + 标价牌，AC Nook 杂货店）-->
<svg viewBox="0 0 71 102" fill="none" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" clip-rule="evenodd" d="M6.35156 37.6318C7.73431 36.0515 10.642 31.3027 11.2109 24.95C11.7798 18.5972 13.7394 14.4805 14.6481 13.2163C15.8525 11.5407 19.9545 6.49634 25.173 5.80196C27.9297 5.43522 34.7153 5.33636 36.9296 10.7419C37.4507 11.7566 38.6388 13.1771 39.2217 10.7419C39.8045 8.30677 41.1465 3.75719 41.7446 1.7868C41.8872 0.650267 42.699 -1.10802 44.8049 0.951045C45.9827 2.21621 46.5858 2.85586 46.7401 3.01754C47.3749 3.68254 47.7481 4.75898 46.0837 5.80196L41.7446 10.3282C41.6745 10.4642 41.6028 10.5951 41.5335 10.7215C40.8661 11.939 40.4245 12.7446 43.8929 13.8254C49.2551 15.4963 49.6473 18.9449 49.4979 25.5929C49.4922 27.7517 48.5101 32.943 44.6272 36.4376C43.9371 37.0587 43.2631 37.6013 42.6144 38.0741C43.2465 37.1271 43.5855 35.8959 43.5855 34.3764C43.5855 30.2947 40.6459 26.818 36.641 26.818C32.6361 26.818 29.7905 29.4779 29.7905 33.5597C29.7905 37.6415 33.1299 40.4599 37.1348 40.4599C37.6446 40.4599 38.1291 40.4275 38.5865 40.3624C37.4973 40.8019 36.6539 40.9844 36.167 41.0122C32.6149 41.2019 24.0809 41.3374 18.3609 40.3618C12.6409 39.5812 7.97135 38.2166 6.35156 37.6318Z" fill="#409B5E"/><path fill-rule="evenodd" clip-rule="evenodd" d="M60.0691 40.8215H67.1206C68.7774 40.8215 70.1206 39.4784 70.1206 37.8215V35.5485C70.1206 33.8917 68.7774 32.5485 67.1206 32.5485H56.7823C56.6085 32.5371 56.4354 32.5373 56.264 32.5485H56.0204C54.9415 32.5485 53.9955 33.1182 53.4669 33.9731C53.1679 34.3324 52.9274 34.7512 52.7651 35.2198L49.1837 45.5611H0V58.4033C0 66.1352 6.26801 72.4033 14 72.4033H36.8272C37.9123 72.4033 38.972 72.2953 39.9964 72.0894L38.9452 75.1249L4.09012 75.1246C2.43326 75.1245 1.09009 76.4677 1.09009 78.1246V80.0307C1.09009 81.6876 2.43324 83.0307 4.09009 83.0307H5.73181C4.69681 84.4841 4.09037 86.2483 4.09037 88.1504C4.09037 93.121 8.23174 97.1504 13.3404 97.1504C18.449 97.1504 22.5904 93.121 22.5904 88.1504C22.5904 86.2483 21.9839 84.4841 20.9489 83.0307H27.7318C26.6968 84.4841 26.0904 86.2483 26.0904 88.1504C26.0904 93.121 30.2317 97.1504 35.3404 97.1504C40.449 97.1504 44.5904 93.121 44.5904 88.1504C44.5904 86.2857 44.0075 84.5535 43.0095 83.1168C44.4915 82.9562 45.8202 81.9654 46.3415 80.46L60.0691 40.8215ZM8.31396 50.0458C8.31396 49.4936 8.76168 49.0458 9.31396 49.0458H11.431C11.9833 49.0458 12.431 49.4936 12.431 50.0458V66.0649C12.431 66.6172 11.9833 67.0649 11.431 67.0649H9.31396C8.76168 67.0649 8.31396 66.6172 8.31396 66.0649V50.0458ZM18.781 49.0458C18.2287 49.0458 17.781 49.4936 17.781 50.0458V66.0649C17.781 66.6172 18.2287 67.0649 18.781 67.0649H20.8981C21.4504 67.0649 21.8981 66.6172 21.8981 66.0649V50.0458C21.8981 49.4936 21.4504 49.0458 20.8981 49.0458H18.781ZM27.2481 50.0458C27.2481 49.4936 27.6958 49.0458 28.2481 49.0458H30.3651C30.9174 49.0458 31.3651 49.4936 31.3651 50.0458V66.0649C31.3651 66.6172 30.9174 67.0649 30.3651 67.0649H28.2481C27.6958 67.0649 27.2481 66.6172 27.2481 66.0649V50.0458ZM37.7151 49.0458C37.1628 49.0458 36.7151 49.4936 36.7151 50.0458V66.0649C36.7151 66.6172 37.1628 67.0649 37.7151 67.0649H39.8322C40.3845 67.0649 40.8322 66.6172 40.8322 66.0649V50.0458C40.8322 49.4936 40.3845 49.0458 39.8322 49.0458H37.7151Z" fill="#4A3D37"/><ellipse cx="12.9968" cy="88.6746" rx="2.99679" ry="2.91579" fill="#F5FCEA"/><ellipse cx="35.49" cy="88.6746" rx="2.91579" ry="2.91579" fill="#F5FCEA"/></svg>

<!-- icon-map（折叠地图 + 大头针）-->
<svg viewBox="0 0 75 96" fill="none" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" clip-rule="evenodd" d="M73.4776 42.6469C74.3136 43.1559 74.8239 44.0639 74.8239 45.0427L74.8238 91.3169C74.8238 93.4535 72.5304 94.8056 70.6609 93.7711L57.1138 86.2752C57.0003 86.2124 56.8922 86.1424 56.79 86.0659C56.6721 86.15 56.5468 86.2257 56.4148 86.2917L38.2551 95.3748C37.2348 95.8851 36.1174 95.7104 35.3137 95.1075L17.4432 85.8943L4.19306 93.4406C2.32316 94.5055 5.73555e-06 93.1551 5.49234e-06 91.0032L0 44.0708C0 43.0455 0.559409 42.1019 1.45893 41.6099L15.2233 34.0814C15.7573 33.5984 16.4698 33.3238 17.2092 33.3496C17.9055 33.3344 18.5765 33.5852 19.0941 34.0247L36.8163 43.2084C36.9627 43.2843 37.1006 43.3721 37.229 43.4702L55.2861 34.1782C55.8761 33.5561 56.7392 33.2153 57.6166 33.3053C58.4756 33.294 59.2896 33.6875 59.8246 34.3346L73.4776 42.6469Z" fill="#F7F3E1"/><path d="M6.52377 82.0731V48.0332L14.8739 42.4786C17.2846 40.5949 18.8918 41.3573 20.3426 42.2992C24.7996 44.5635 34.0799 49.3192 35.5458 50.2277C37.0117 51.1363 38.7848 50.6063 39.4881 50.2277C43.6624 47.9953 52.6388 43.2341 55.1504 42.0491C56.9845 41.0167 58.6538 41.4091 59.4364 42.0491L68.3122 47.8817C68.3025 58.2423 68.2888 79.5382 68.3122 81.8371C68.3355 84.136 66.3569 83.6355 65.3647 83.0979C63.993 82.2183 60.8645 80.1598 59.3237 78.9635C57.8528 77.6733 55.9286 78.4259 55.1504 78.9635C50.6606 81.3572 41.2617 86.3554 39.5842 87.1988C37.9067 88.0422 36.1662 87.5502 35.5056 87.1988L19.857 78.9518C17.6359 77.4557 16.3856 78.4383 15.5177 78.9518C14.6499 79.4653 12.4948 80.8085 10.8174 82.0731C7.10015 84.4843 6.40613 83.0778 6.52377 82.0731Z" fill="#59C9C0"/><path fill-rule="evenodd" clip-rule="evenodd" d="M54.0825 34.8346C57.006 31.1652 58.7544 26.5095 58.7544 21.4436C58.7544 9.60064 49.1992 0 37.4123 0C25.6255 0 16.0703 9.60064 16.0703 21.4436C16.0703 26.5613 17.8546 31.2602 20.8321 34.9467L34.047 57.2165C35.5684 59.7805 39.2797 59.7805 40.8011 57.2165L54.0825 34.8346Z" fill="#4C3C33"/><ellipse cx="37.4124" cy="22.2135" rx="7.25612" ry="7.29066" fill="#F7F2D9"/></svg>

<!-- icon-miles（NookMiles 五角星 + 蓝色行星圆 + 草地装饰）-->
<svg viewBox="0 0 94 104" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M18.5704 51.2672C20.1382 47.5048 23.6707 45.5342 25.4343 45.0117C23.6734 43.9782 19.9354 40.3391 19.6027 32.9953C10.7666 33.4922 5.22798 29.0074 3.5632 26.7029C-2.5944 15.7943 1.2284 11.8209 2.05308 10.3364C2.87776 8.85203 6.20221 6.93342 10.1607 7.31827C14.1191 7.70312 16.5932 10.7269 15.8785 18.6439C15.3067 24.9774 18.123 26.6555 19.6027 26.7029C19.8497 12.3783 38.5262 7.17892 47.8605 6.22668H61.0065C60.9316 6.2097 60.6876 5.45528 60.4309 3.52975C60.1682 1.55967 62.4557 0.355715 63.6323 0H68.3112C70.1828 0 71.2433 1.97008 71.5396 2.95512V7.91826C77.8629 8.68473 84.0869 15.4689 84.3665 20.3155C84.5902 24.1928 87.4859 25.5401 88.9058 25.7291H93.0804C93.7846 37.2469 78.5536 44.5877 77.6498 44.7615C76.9268 44.9005 77.1294 45.4489 77.3211 45.7057C79.28 46.7111 83.2015 49.5912 83.2015 51.3733C82.7428 58.0241 77.2769 58.0624 74.6013 57.2501C71.5912 55.8884 64.625 53.0216 60.8409 52.4483C56.1107 51.7316 46.0054 53.0933 42.852 53.165C40.3293 53.2223 30.7041 56.9351 26.2069 58.7843C24.0484 59.2616 18.5704 55.5783 18.5704 51.2672Z" fill="#4D3E36"/><path fill-rule="evenodd" clip-rule="evenodd" d="M83.6368 17.5752C84.0789 18.2418 85.027 20.004 85.2826 21.7197C85.5381 23.4354 86.4233 24.3206 86.834 24.5488C85.4347 27.7886 81.6503 34.3047 77.7078 34.4507C72.7796 34.6333 71 34.5876 68.9466 31.0284C66.8932 27.4692 67.6234 22.541 70.9088 20.4876C73.8911 18.6237 77.4701 18.2392 82.1689 17.7345C82.6465 17.6832 83.1356 17.6307 83.6368 17.5752ZM78.2167 29.6498C79.8959 29.6498 81.2571 28.2886 81.2571 26.6094C81.2571 24.9303 79.8959 23.569 78.2167 23.569C76.5376 23.569 75.1763 24.9303 75.1763 26.6094C75.1763 28.2886 76.5376 29.6498 78.2167 29.6498Z" fill="#EBCD55"/><path d="M5.08344 18.6624C3.47727 17.6914 1.32688 18.6341 0.452449 19.2268C0.204931 17.7093 -0.207434 14.3276 0.123246 12.9404C0.536596 11.2065 3.1348 7.27628 8.9217 6.9295C13.5512 6.65207 15.5747 10.4359 16.0077 12.3625V18.2C15.4172 18.9899 13.5748 20.5928 10.9294 20.6853C7.6226 20.8009 7.09115 19.8761 5.08344 18.6624Z" fill="#EBCD55"/><circle cx="47.2224" cy="104.709" r="45.8825" fill="#B3BFFF"/></svg>

<!-- icon-diy（DIY 锤子 + 工坊；用作"建造/创造"语义）-->
<!-- 完整 SVG 见仓库 src/assets/img/icons/icon-diy.svg；2.9 KB，五个 path 元素 -->

<!-- icon-helicopter（直升机；"运输/出岛"语义）-->
<!-- 完整 SVG 见仓库 src/assets/img/icons/icon-helicopter.svg；4.3 KB -->

<!-- icon-critterpedia（昆虫图鉴；"知识库/百科"语义）-->
<!-- 完整 SVG 见仓库 src/assets/img/icons/icon-critterpedia.svg；4.4 KB -->

<!-- icon-variant（变体本/物品卡；"清单/收藏"语义）-->
<svg viewBox="0 0 58 85" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M43.8907 85.0015H5.12987C1.05125 85.0015 0.0105313 83.046 0 82.0683V15.7754C16.9292 15.5687 51.7093 15.1553 55.3968 15.1553C57.2095 15.1553 57.6627 18.9073 57.6627 20.7833V71.7283C57.6627 81.3179 48.4813 84.5728 43.8907 85.0015Z" fill="#F9F6E5"/><path d="M0.00825713 4.36744V81.6563C0.00825713 81.9107 1.72555 79.3995 4.65238 79.419C13.8934 79.4804 34.6044 79.5664 43.52 79.419C52.4355 79.2716 55.72 70.7617 56.2478 66.5252V12.6237C55.7692 3.76914 48.2707 0.518499 44.5813 0H6.47088C1.01456 0 -0.111003 2.91163 0.00825713 4.36744Z" fill="#4C3C33"/><rect x="14.6953" y="52.9385" width="26.8576" height="6.07836" rx="1.77051" fill="#5AA15B"/><rect x="10.4053" y="62.7178" width="36.8525" height="6.31173" rx="2.95084" fill="#5AA15B"/></svg>
```

> diy / helicopter / critterpedia 三个图标体积偏大（2.9–4.4 KB），如确实要用，直接从仓库 `src/assets/img/icons/<name>.svg` 复制 SVG 整段内容粘贴到 `cnode-icon` 容器即可。视觉风格与上述 7 个统一。

**通用调用模板**：

```html
<div class="cnode-icon">
  <svg viewBox="..." fill="none" xmlns="http://www.w3.org/2000/svg">
    <!-- AC icon path 内容 -->
  </svg>
</div>
```

---

## 常用内联 SVG 图标库

卡片图标统一规格：`width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="#fff" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"`

| 用途 | SVG path |
|---|---|
| 叶子 / 自然 | `<path d="M11 20A7 7 0 0 1 9.8 6.1C15.5 5 17 4.48 19 2c1 2 2 4.18 2 8 0 5.5-4.78 10-10 10z"/><path d="M2 21c0-3 1.85-5.36 5.08-6C9.5 14.52 12 13 13 12"/>` |
| 闪电 / 快速 | `<polygon points="13 2 3 14 12 14 11 22 21 10 12 10 13 2"/>` |
| 文件 / 文档 | `<path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/>` |
| 上传 / 导出 | `<path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="17 8 12 3 7 8"/><line x1="12" y1="3" x2="12" y2="15"/>` |
| 调色盘 | `<circle cx="13.5" cy="6.5" r="1.5" fill="#fff"/><circle cx="17.5" cy="10.5" r="1.5" fill="#fff"/><circle cx="8.5" cy="7.5" r="1.5" fill="#fff"/><circle cx="6.5" cy="12.5" r="1.5" fill="#fff"/><path d="M12 2C6.5 2 2 6.5 2 12s4.5 10 10 10c.926 0 1.648-.746 1.648-1.688 0-.437-.18-.835-.437-1.125-.29-.289-.438-.652-.438-1.125 0-.92.75-1.688 1.688-1.688H16c2.75 0 5-2.25 5-5 0-4.42-4.03-8-9-8z"/>` |
| 星形 / 收藏 | `<polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/>` |
| 对勾 / 完成 | `<polyline points="20 6 9 17 4 12"/>` |
| 向右箭头 | `<polyline points="9 18 15 12 9 6"/>` |
| 手柄 / 游戏 | `<rect x="2" y="7" width="20" height="13" rx="4"/><path d="M7 12h4M9 10v4"/><circle cx="16" cy="12" r="1" fill="#fff"/><circle cx="19" cy="12" r="1" fill="#fff"/>` |
| 图表 / 数据 | `<line x1="18" y1="20" x2="18" y2="10"/><line x1="12" y1="20" x2="12" y2="4"/><line x1="6" y1="20" x2="6" y2="14"/>` |
| 用户 / 团队 | `<path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M23 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/>` |

---

## 季节子主题（Seasonal Themes — 可叠加在任意布局上）

**何时使用**：用户提到春/樱花/三月发布 → spring；夏/海岛/暑期/活力 → summer；秋/枫叶/复盘/收获 → autumn；冬/年终/年会/圣诞/雪 → winter。也可不选，保持默认羊皮纸 + 岛屿绿（年中通用）。

**如何启用**（三步）：
1. 在 `<body>` 加 `data-theme="spring|summer|autumn|winter"`
2. 将通用容器 CSS + 对应季节的 CSS 追加到 BASE `<style>` 末尾
3. 将对应季节的 HTML 装饰块插入到 `<div class="deck">` 之后（与导航箭头同级）

> 所有飘落 / 漂浮装饰自动遵守 `prefers-reduced-motion: reduce`，关闭动画后只保留半透明静止图。

### 通用容器（4 季共用，先加一次）

```css
/* 追加到 BASE <style> 末尾 */
.season-decor { pointer-events: none; }
.season-decor > * {
  position: fixed; pointer-events: none;
  z-index: 2; will-change: transform, opacity;
}
@media (prefers-reduced-motion: reduce) {
  .season-decor > * { animation: none !important; opacity: 0.4; }
}
```

---

### 春樱 SPRING — 樱花飘落

```css
[data-theme="spring"] {
  --bg-parchment:  #fef5f0;
  --bg-card:       rgb(252, 244, 240);
  --bg-mint-light: #ffe6ec;

  --text-primary:   #6d3f4a;
  --text-body:      #7e4f5c;
  --text-secondary: #a78491;
  --text-muted:     #b59ba5;

  --accent-teal:    #ec9bb6;
  --accent-teal-a:  #d57f9c;
  --accent-yellow:  #f5c331;
  --accent-yellow-d:#daa820;
  --green:          #88c999;

  --border-std:   2px solid #c9a8b3;
  --shadow-btn:   0 5px 0 0 #c9a8b3;
  --shadow-btn-h: 0 6px 0 0 #c9a8b3;
  --shadow-btn-a: 0 1px 0 0 #c9a8b3;
  --shadow-card:  0 4px 10px rgba(140, 80, 100, 0.28);
}

/* 樱花瓣（8 片，渐次飘落）*/
[data-theme="spring"] .petal {
  top: -30px; width: 14px; height: 14px;
  background: radial-gradient(circle at 30% 30%, #ffd0dc 0%, #ec9bb6 70%);
  border-radius: 100% 0 100% 0;
  opacity: 0.55;
  animation: petalFall linear infinite;
}
[data-theme="spring"] .petal:nth-child(1) { left:  6%; --s: 1;    animation-duration: 12s;   animation-delay: -2s;  }
[data-theme="spring"] .petal:nth-child(2) { left: 18%; --s: 0.85; animation-duration: 14s;   animation-delay: -5s;  }
[data-theme="spring"] .petal:nth-child(3) { left: 32%; --s: 1.15; animation-duration: 11s;   animation-delay: -8s;  }
[data-theme="spring"] .petal:nth-child(4) { left: 47%; --s: 0.75; animation-duration: 13s;   animation-delay: -1s;  }
[data-theme="spring"] .petal:nth-child(5) { left: 60%; --s: 1;    animation-duration: 15s;   animation-delay: -10s; }
[data-theme="spring"] .petal:nth-child(6) { left: 73%; --s: 0.9;  animation-duration: 12.5s; animation-delay: -6s;  }
[data-theme="spring"] .petal:nth-child(7) { left: 86%; --s: 1.05; animation-duration: 13.5s; animation-delay: -3s;  }
[data-theme="spring"] .petal:nth-child(8) { left: 94%; --s: 0.7;  animation-duration: 16s;   animation-delay: -9s;  }

@keyframes petalFall {
  0%   { transform: translate(0, -10vh)  rotate(0deg)   scale(var(--s,1)); opacity: 0; }
  10%  { opacity: 0.65; }
  90%  { opacity: 0.5; }
  100% { transform: translate(80px, 110vh) rotate(540deg) scale(var(--s,1)); opacity: 0; }
}
```

```html
<!-- 紧贴 <div class="deck"> 之后 -->
<div class="season-decor" aria-hidden="true">
  <span class="petal"></span><span class="petal"></span>
  <span class="petal"></span><span class="petal"></span>
  <span class="petal"></span><span class="petal"></span>
  <span class="petal"></span><span class="petal"></span>
</div>
```

---

### 夏海 SUMMER — 海浪与海鸥

> 颜色微调（更冷的沙滩米 + 更亮的太阳黄），主要靠装饰营造海岛氛围。

```css
[data-theme="summer"] {
  --bg-parchment:  #f0f4e8;
  --bg-card:       rgb(245, 248, 232);
  --bg-mint-light: #d6f0eb;

  --text-primary:   #2a5260;
  --text-body:      #3a657a;
  --text-secondary: #6b8b95;
  --text-muted:     #82a0a8;

  --accent-teal:    #19c8b9;
  --accent-teal-a:  #11a89b;
  --accent-yellow:  #ffd166;
  --accent-yellow-d:#e8b850;
  --green:          #6fba2c;

  --border-std:   2px solid #95b0a8;
  --shadow-btn:   0 5px 0 0 #95b0a8;
  --shadow-btn-h: 0 6px 0 0 #95b0a8;
  --shadow-btn-a: 0 1px 0 0 #95b0a8;
  --shadow-card:  0 4px 10px rgba(50, 90, 110, 0.22);
}

/* 底部海浪（持续右移）*/
[data-theme="summer"] .ocean-wave {
  left: 0; right: 0; bottom: -10px;
  height: 90px;
  background:
    url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1440 100' preserveAspectRatio='none'><path d='M0,50 C240,90 480,10 720,50 C960,90 1200,10 1440,50 L1440,100 L0,100 Z' fill='%2319c8b9' opacity='0.18'/></svg>") repeat-x;
  background-size: 1440px 90px;
  animation: waveDrift 18s linear infinite;
  opacity: 0.9;
}
@keyframes waveDrift { to { background-position: -1440px 0; } }

/* 海鸥（2 只，缓慢横飞）*/
[data-theme="summer"] .seagull {
  width: 28px; height: 14px; color: rgba(45, 60, 75, 0.42);
  animation-timing-function: linear; animation-iteration-count: infinite;
}
[data-theme="summer"] .seagull svg { width: 100%; height: 100%; }
[data-theme="summer"] .seagull-1 { top: 16%; left: -6%; --s: 1;    animation-name: seagullFly; animation-duration: 26s; animation-delay: -3s;  }
[data-theme="summer"] .seagull-2 { top: 24%; left: -6%; --s: 0.75; animation-name: seagullFly; animation-duration: 32s; animation-delay: -14s; }

@keyframes seagullFly {
  0%   { transform: translate(0, 0)        scale(var(--s,1)); }
  50%  { transform: translate(50vw, -18px) scale(var(--s,1)); }
  100% { transform: translate(110vw, 0)    scale(var(--s,1)); }
}
```

```html
<div class="season-decor" aria-hidden="true">
  <div class="ocean-wave"></div>
  <div class="seagull seagull-1">
    <svg viewBox="0 0 28 14" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
      <path d="M2,11 Q7,2 12,8 Q18,2 26,11"/>
    </svg>
  </div>
  <div class="seagull seagull-2">
    <svg viewBox="0 0 28 14" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
      <path d="M2,11 Q7,2 12,8 Q18,2 26,11"/>
    </svg>
  </div>
</div>
```

---

### 秋收 AUTUMN — 枫叶飘落

```css
[data-theme="autumn"] {
  --bg-parchment:  #f5ecd6;
  --bg-card:       rgb(247, 240, 220);
  --bg-mint-light: #ffe8c4;

  --text-primary:   #5a2b1a;
  --text-body:      #6e3f2a;
  --text-secondary: #9a7654;
  --text-muted:     #a98a6a;

  --accent-teal:    #d97734;
  --accent-teal-a:  #b85f25;
  --accent-yellow:  #e8b04c;
  --accent-yellow-d:#c89438;
  --green:          #8a9a3b;

  --border-std:   2px solid #b89a72;
  --shadow-btn:   0 5px 0 0 #b89a72;
  --shadow-btn-h: 0 6px 0 0 #b89a72;
  --shadow-btn-a: 0 1px 0 0 #b89a72;
  --shadow-card:  0 4px 10px rgba(120, 60, 30, 0.32);
}

/* 枫叶（8 片，4 色循环，旋转下落）*/
[data-theme="autumn"] .leaf {
  top: -30px; width: 18px; height: 18px;
  background-repeat: no-repeat; background-position: center; background-size: contain;
  opacity: 0.65;
  animation: leafFall linear infinite;
}
[data-theme="autumn"] .leaf-orange { background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'><path d='M12 1.5L13.6 5.5L18 4.5L16 9L21 10.5L16.5 13L19.5 17L14 16L13.5 21L12 18L10.5 21L10 16L4.5 17L7.5 13L3 10.5L8 9L6 4.5L10.4 5.5L12 1.5Z' fill='%23d97734'/></svg>"); }
[data-theme="autumn"] .leaf-red    { background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'><path d='M12 1.5L13.6 5.5L18 4.5L16 9L21 10.5L16.5 13L19.5 17L14 16L13.5 21L12 18L10.5 21L10 16L4.5 17L7.5 13L3 10.5L8 9L6 4.5L10.4 5.5L12 1.5Z' fill='%23b04428'/></svg>"); }
[data-theme="autumn"] .leaf-gold   { background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'><path d='M12 1.5L13.6 5.5L18 4.5L16 9L21 10.5L16.5 13L19.5 17L14 16L13.5 21L12 18L10.5 21L10 16L4.5 17L7.5 13L3 10.5L8 9L6 4.5L10.4 5.5L12 1.5Z' fill='%23e8b04c'/></svg>"); }
[data-theme="autumn"] .leaf-brown  { background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'><path d='M12 1.5L13.6 5.5L18 4.5L16 9L21 10.5L16.5 13L19.5 17L14 16L13.5 21L12 18L10.5 21L10 16L4.5 17L7.5 13L3 10.5L8 9L6 4.5L10.4 5.5L12 1.5Z' fill='%237a3e1f'/></svg>"); }

[data-theme="autumn"] .leaf:nth-child(1) { left:  5%; --s: 1;    animation-duration: 13s;   animation-delay: -2s;  }
[data-theme="autumn"] .leaf:nth-child(2) { left: 19%; --s: 0.8;  animation-duration: 15s;   animation-delay: -7s;  }
[data-theme="autumn"] .leaf:nth-child(3) { left: 33%; --s: 1.2;  animation-duration: 12s;   animation-delay: -10s; }
[data-theme="autumn"] .leaf:nth-child(4) { left: 48%; --s: 0.85; animation-duration: 14s;   animation-delay: -3s;  }
[data-theme="autumn"] .leaf:nth-child(5) { left: 62%; --s: 1.05; animation-duration: 16s;   animation-delay: -12s; }
[data-theme="autumn"] .leaf:nth-child(6) { left: 75%; --s: 0.95; animation-duration: 13.5s; animation-delay: -5s;  }
[data-theme="autumn"] .leaf:nth-child(7) { left: 87%; --s: 0.75; animation-duration: 15.5s; animation-delay: -9s;  }
[data-theme="autumn"] .leaf:nth-child(8) { left: 95%; --s: 1.1;  animation-duration: 12.5s; animation-delay: -1s;  }

@keyframes leafFall {
  0%   { transform: translate(0, -10vh)    rotate(0deg)   scale(var(--s,1)); opacity: 0; }
  10%  { opacity: 0.7; }
  50%  { transform: translate(40px, 50vh)  rotate(360deg) scale(var(--s,1)); }
  90%  { opacity: 0.6; }
  100% { transform: translate(-30px, 110vh) rotate(720deg) scale(var(--s,1)); opacity: 0; }
}
```

```html
<div class="season-decor" aria-hidden="true">
  <span class="leaf leaf-orange"></span><span class="leaf leaf-red"></span>
  <span class="leaf leaf-gold"></span><span class="leaf leaf-brown"></span>
  <span class="leaf leaf-orange"></span><span class="leaf leaf-red"></span>
  <span class="leaf leaf-gold"></span><span class="leaf leaf-brown"></span>
</div>
```

---

### 冬雪 WINTER — 雪花飘落

```css
[data-theme="winter"] {
  --bg-parchment:  #f4f8fb;
  --bg-card:       rgb(248, 250, 252);
  --bg-mint-light: #dce8f0;

  --text-primary:   #1e3a4f;
  --text-body:      #324f64;
  --text-secondary: #6a8090;
  --text-muted:     #8ea3b3;

  --accent-teal:    #7fc1e8;
  --accent-teal-a:  #5fa9d5;
  --accent-yellow:  #f4a261;
  --accent-yellow-d:#d98c45;
  --green:          #2f6645;

  --border-std:   2px solid #a8c0d4;
  --shadow-btn:   0 5px 0 0 #a8c0d4;
  --shadow-btn-h: 0 6px 0 0 #a8c0d4;
  --shadow-btn-a: 0 1px 0 0 #a8c0d4;
  --shadow-card:  0 4px 10px rgba(40, 70, 100, 0.22);
}

/* 雪花（12 片，缓慢下落 + 微摆动；尺寸用 --s 变量分层）*/
[data-theme="winter"] .snow {
  top: -20px; width: 6px; height: 6px; border-radius: 50%;
  background: #ffffff;
  box-shadow: 0 0 8px rgba(127, 193, 232, 0.85);
  opacity: 0.7;
  animation: snowFall linear infinite;
}
[data-theme="winter"] .snow:nth-child(1)  { left:  4%; --s: 1;    animation-duration: 12s;   animation-delay: -1s;  }
[data-theme="winter"] .snow:nth-child(2)  { left: 12%; --s: 0.7;  animation-duration: 16s;   animation-delay: -5s;  }
[data-theme="winter"] .snow:nth-child(3)  { left: 21%; --s: 1.3;  animation-duration: 10s;   animation-delay: -8s;  }
[data-theme="winter"] .snow:nth-child(4)  { left: 30%; --s: 0.8;  animation-duration: 14s;   animation-delay: -3s;  }
[data-theme="winter"] .snow:nth-child(5)  { left: 39%; --s: 1.1;  animation-duration: 11s;   animation-delay: -11s; }
[data-theme="winter"] .snow:nth-child(6)  { left: 48%; --s: 0.6;  animation-duration: 18s;   animation-delay: -6s;  }
[data-theme="winter"] .snow:nth-child(7)  { left: 57%; --s: 1;    animation-duration: 13s;   animation-delay: -2s;  }
[data-theme="winter"] .snow:nth-child(8)  { left: 66%; --s: 0.9;  animation-duration: 15s;   animation-delay: -9s;  }
[data-theme="winter"] .snow:nth-child(9)  { left: 75%; --s: 1.2;  animation-duration: 12s;   animation-delay: -4s;  }
[data-theme="winter"] .snow:nth-child(10) { left: 83%; --s: 0.75; animation-duration: 17s;   animation-delay: -10s; }
[data-theme="winter"] .snow:nth-child(11) { left: 91%; --s: 1.05; animation-duration: 11.5s; animation-delay: -7s;  }
[data-theme="winter"] .snow:nth-child(12) { left: 97%; --s: 0.85; animation-duration: 14.5s; animation-delay: -12s; }

@keyframes snowFall {
  0%   { transform: translate(0, -10vh)   scale(var(--s,1)); opacity: 0; }
  10%  { opacity: 0.85; }
  25%  { transform: translate(8px, 25vh)  scale(var(--s,1)); }
  50%  { transform: translate(-6px, 50vh) scale(var(--s,1)); }
  75%  { transform: translate(10px, 75vh) scale(var(--s,1)); }
  90%  { opacity: 0.7; }
  100% { transform: translate(0, 110vh)   scale(var(--s,1)); opacity: 0; }
}
```

```html
<div class="season-decor" aria-hidden="true">
  <span class="snow"></span><span class="snow"></span><span class="snow"></span>
  <span class="snow"></span><span class="snow"></span><span class="snow"></span>
  <span class="snow"></span><span class="snow"></span><span class="snow"></span>
  <span class="snow"></span><span class="snow"></span><span class="snow"></span>
</div>
```

---

### 与 Layout 的搭配建议

| 主题 | 强搭配 layout | 适用场景 |
|---|---|---|
| spring  | Cover, Island Scene, Pull Quote, Color Wheel              | 春季发布、品牌焕新、年初规划 |
| summer  | Cover, Island Scene, Big Stat Hero, Stat Strip            | 暑期发布会、夏令营、活力主题 |
| autumn  | Section Divider, Timeline, FAQ, Quote                     | 复盘、年中总结、回顾型内容 |
| winter  | Cover, Section Divider, Stat Strip, Comparison            | 年终总结、年会汇报、回首与展望 |

### 与现有氛围工具组合

- **Time HUD**：4 季皆可叠加，颜色自动跟随 `--text-primary` token，无需调整
- **AC Cursor**：黄色光标在冬季冷调背景反差最强；夏季背景偏黄，反差较弱
- **Divider 花纹**：5 种皆兼容；偏好：spring → wave-yellow，autumn → brown，winter → teal
