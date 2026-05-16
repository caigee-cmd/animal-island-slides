# Animal Island Slides — Animation Patterns

**何时读取**：Phase 3 生成幻灯片时，如需添加入场动画或交互效果，参考本文件选择合适的模式。

---

## 核心机制（不可绕过）

所有入场动画必须通过 `.slide.entered .element` 驱动 transition，**禁止**直接在元素上写 `animation:`（浏览器在不可见父元素内会跳过 animation）。

JS 引擎在切换到某张幻灯片时先加 `.active`，再经过 double-rAF 加 `.entered`，给 transition 一个触发时机。

**唯一例外**：纯装饰性背景元素（如树叶飘动）可以直接用 `animation: keyframes infinite`，因为它们不需要与幻灯片进入时机同步。

---

## 缓动曲线

```css
--ease-ac: cubic-bezier(0.4, 0, 0.2, 1);  /* 标准缓动，几乎所有 transition 都用这个 */
```

---

## 入场模式

### 模式 A — 淡入上浮（最常用）

适用于：标题、文字块、卡片、列表

```css
.element {
  opacity: 0;
  transform: translateY(14px);          /* 偏移量：header 用 12–14px，hero 用 16px */
  transition: opacity 0.4s var(--ease-ac) 0.05s,
              transform 0.4s var(--ease-ac) 0.05s;
}
.slide.entered .element       { opacity: 1; transform: translateY(0); }
.slide:not(.entered) .element { transition-delay: 0s; }  /* 离开时立即复位 */
```

**时长参考**：
- 标题 / header：0.4s，delay 0.05s
- 正文 / 内容块：0.45–0.5s，delay 0.1–0.2s
- Hero 大字：0.6s，delay 0.15s
- 补充说明：0.45s，delay 0.3–0.4s

---

### 模式 B — 交错入场（列表 / 卡片序列）

适用于：Card Grid、Timeline、Stat Strip、Process Flow、FAQ

```css
.item {
  opacity: 0;
  transform: translateY(14px);
  transition: opacity 0.45s var(--ease-ac), transform 0.45s var(--ease-ac);
}
.slide.entered .item              { opacity: 1; transform: translateY(0); }
.slide.entered .item:nth-child(1) { transition-delay: 0.18s; }
.slide.entered .item:nth-child(2) { transition-delay: 0.28s; }
.slide.entered .item:nth-child(3) { transition-delay: 0.38s; }
.slide.entered .item:nth-child(4) { transition-delay: 0.48s; }
.slide.entered .item:nth-child(5) { transition-delay: 0.58s; }
.slide:not(.entered) .item { transition-delay: 0s; }
```

**卡片（6个）的更密集间隔**：
```css
.slide.entered .ac-card:nth-child(1) { transition-delay: 0.10s; }
.slide.entered .ac-card:nth-child(2) { transition-delay: 0.17s; }
.slide.entered .ac-card:nth-child(3) { transition-delay: 0.24s; }
/* 依此类推，间隔 0.07s */
```

---

### 模式 C — 左右滑入（两列 / 对比 / 手机模拟）

适用于：Two Column、Comparison、NookPhone Showcase

```css
/* 左侧内容从左滑入 */
.col-left {
  opacity: 0;
  transform: translateX(-16px);          /* 幅度可用 -16px 或 -24px */
  transition: opacity 0.45s var(--ease-ac) 0.1s,
              transform 0.45s var(--ease-ac) 0.1s;
}
.slide.entered .col-left       { opacity: 1; transform: translateX(0); }
.slide:not(.entered) .col-left { transition-delay: 0s; }

/* 右侧内容从右滑入 */
.col-right {
  opacity: 0;
  transform: translateX(16px);
  transition: opacity 0.45s var(--ease-ac) 0.2s,
              transform 0.45s var(--ease-ac) 0.2s;
}
.slide.entered .col-right       { opacity: 1; transform: translateX(0); }
.slide:not(.entered) .col-right { transition-delay: 0s; }
```

---

### 模式 D — 缩放淡入（强调型）

适用于：Big Stat Hero 大数字、Code Slide 代码卡、Pull Quote 引号装饰

```css
/* 数字 hero */
.stat-hero {
  opacity: 0;
  transform: scale(0.92);
  transition: opacity 0.6s var(--ease-ac) 0.15s,
              transform 0.6s var(--ease-ac) 0.15s;
}
.slide.entered .stat-hero { opacity: 1; transform: scale(1); }

/* 代码卡（含轻微位移）*/
.code-card {
  opacity: 0;
  transform: translateY(16px) scale(0.98);
  transition: opacity 0.5s var(--ease-ac) 0.2s,
              transform 0.5s var(--ease-ac) 0.2s;
}
.slide.entered .code-card { opacity: 1; transform: translateY(0) scale(1); }

/* 引号装饰（从较小比例弹入）*/
.pullquote-mark {
  opacity: 0;
  transform: scale(0.7);
  transition: opacity 0.5s var(--ease-ac) 0.1s,
              transform 0.5s var(--ease-ac) 0.1s;
}
.slide.entered .pullquote-mark { opacity: 0.85; transform: scale(1); }
```

---

### 模式 E — 纯淡入（无位移）

适用于：箭头、分隔线等轻量连接元素

```css
.process-arrow {
  opacity: 0;
  transition: opacity 0.4s var(--ease-ac);
}
.slide.entered .process-arrow              { opacity: 0.75; }
.slide.entered .process-arrow:nth-of-type(1) { transition-delay: 0.24s; }
.slide.entered .process-arrow:nth-of-type(2) { transition-delay: 0.36s; }
```

---

## 背景装饰动画（允许直接用 `animation:`）

这类元素纯装饰、在幻灯片加载后就持续循环，不依赖 `.entered` 触发时机：

```css
/* 树叶飘动 */
@keyframes leafFloat {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50%       { transform: translateY(-10px) rotate(6deg); }
}
.leaf { animation: leafFloat 4s ease-in-out infinite; }
.leaf-1 { animation-delay: 0s; }
.leaf-2 { animation-delay: 1.5s; }
.leaf-3 { animation-delay: 0.8s; }
.leaf-4 { animation-delay: 2.2s; }

/* 树叶摆动（更小幅度）*/
@keyframes leafWiggle {
  0%, 100% { transform: rotate(15deg); }
  50%       { transform: rotate(-10deg); }
}

/* 时钟冒号闪烁 */
@keyframes phoneBlink {
  0%, 50%    { opacity: 1; }
  51%, 100%  { opacity: 0; }
}
.blink { animation: phoneBlink 1s steps(1) infinite; }
```

---

## 交互动画（hover / active）

```css
/* 按钮按压感 */
.btn {
  box-shadow: var(--shadow-btn);        /* 0 5px 0 0 #bdaea0 */
  transition: all 0.25s var(--ease-ac);
}
.btn:hover  { box-shadow: var(--shadow-btn-h); transform: translateY(-1px); }
.btn:active { box-shadow: var(--shadow-btn-a); transform: translateY(2px); }

/* 卡片悬浮上浮 */
.ac-card:hover { transform: translateY(-4px); box-shadow: 0 12px 28px rgba(107,92,67,0.18); }

/* 列表项横移 */
.bullet-item:hover { transform: translateX(6px); border-color: var(--accent-yellow); }
.check-item:hover  { transform: translateX(4px); border-color: var(--accent-teal); }
```

---

## 时序速查

| 元素角色 | 时长 | delay |
|---|---|---|
| 装饰徽章 / eyebrow | 0.4–0.5s | 0.05s |
| 主标题 | 0.5s | 0.1–0.15s |
| 副标题 / 引言 | 0.45s | 0.2–0.25s |
| Hero 大数字 / 图片 | 0.6s | 0.15s |
| 正文 / 列表块 | 0.4–0.45s | 0.1–0.2s |
| 列表第 1 项 | 0.45s | 0.18s |
| 列表第 N 项 | 0.45s | 0.18s + (N-1)×0.10s |
| 卡片第 N 张（密集） | 0.4s | 0.10s + (N-1)×0.07s |
| 补充说明 / caption | 0.45s | 0.35–0.5s |
| hover 响应 | 0.2–0.25s | — |
