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

## LAYOUT A — Cover（封面 / Island Welcome）

**使用场景**：演示文稿第一张，或各章节的大标题页。  
**背景**：岛屿绿 `--bg-green`，底部椭圆波浪露出羊皮纸色。

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

  <div style="position:relative;z-index:1;display:flex;flex-direction:column;align-items:center;">
    <div class="cover-badge"><!-- 副标签文字，如"第一章" / 项目名 --></div>
    <h1 class="cover-title"><!-- 主标题，<em> 包裹高亮词 --></h1>
    <p class="cover-subtitle"><!-- 副标题或 tagline --></p>
    <button class="cover-btn" onclick="next()">
      <!-- 按钮文字，如"开始" / "下一节" -->
      <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="9 18 15 12 9 6"/></svg>
    </button>
  </div>
</section>
```

---

## LAYOUT B — Card Grid（功能卡片 / NookPhone Grid）

**使用场景**：功能列表、对比项目、团队成员、产品特性。最多 6 张卡片。  
**背景**：羊皮纸 `--bg-parchment`。

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
    <div class="divider-num">Chapter 02</div>
    <h2 class="divider-title"><!-- 章节标题 --></h2>
    <p class="divider-desc"><!-- 一句话简介，可省略 --></p>
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
