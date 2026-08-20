---
tags:
  - vue
  - css
  - reference
created: 2026-06-01
updated: 2026-06-01
---

# CSS 属性速查手册

## 一、布局

### display — 显示类型

| 值 | 含义 | 典型场景 |
|---|------|---------|
| `block` | 块级，独占一行 | `div`、`p`、`h1` 默认 |
| `inline` | 行内，不能设宽高 | `span`、`a` 默认 |
| `inline-block` | 行内块，可设宽高 | 按钮、标签并排 |
| `flex` | 弹性布局 | 一维排列（最常用） |
| `grid` | 网格布局 | 二维布局 |
| `none` | 不显示，不占空间 | 隐藏元素 |
| `table` | 表格布局 | 特殊场景 |

### position — 定位

| 值 | 含义 | 参照物 |
|---|------|-------|
| `static` | 默认，正常流 | 无 |
| `relative` | 相对定位 | 自身原始位置 |
| `absolute` | 绝对定位 | 最近的 `relative/absolute/fixed` 祖先 |
| `fixed` | 固定定位 | 视口 |
| `sticky` | 粘性定位 | 滚动容器 + 视口 |

```css
/* 粘性导航：滚动到顶部时吸住 */
.nav {
  position: sticky;
  top: 0;
  z-index: 100;
}
```

### float — 浮动（旧方案，新项目用 flex/grid 替代）

| 值 | 含义 |
|---|------|
| `left` | 左浮动 |
| `right` | 右浮动 |
| `none` | 不浮动（默认） |

---

## 二、Flexbox 弹性布局

### 容器属性（设在父元素上）

```css
.container {
  display: flex;

  /* 主轴方向 */
  flex-direction: row;            /* → 水平（默认） */
  flex-direction: row-reverse;    /* ← 水平反向 */
  flex-direction: column;         /* ↓ 垂直 */
  flex-direction: column-reverse; /* ↑ 垂直反向 */

  /* 换行 */
  flex-wrap: nowrap;   /* 不换行（默认） */
  flex-wrap: wrap;     /* 换行 */

  /* 主轴对齐 */
  justify-content: flex-start;    /* 靠左（默认） */
  justify-content: flex-end;      /* 靠右 */
  justify-content: center;        /* 居中 */
  justify-content: space-between; /* 两端对齐，中间等分 */
  justify-content: space-around;  /* 每项两侧等距 */
  justify-content: space-evenly;  /* 完全等分 */

  /* 交叉轴对齐 */
  align-items: stretch;    /* 拉伸填满（默认） */
  align-items: flex-start; /* 顶部对齐 */
  align-items: center;     /* 垂直居中 */
  align-items: flex-end;   /* 底部对齐 */

  /* 多行对齐（需要 flex-wrap: wrap） */
  align-content: flex-start;
  align-content: center;
  align-content: space-between;

  /* 间距 */
  gap: 8px;          /* 行列间距都是 8px */
  gap: 8px 16px;     /* 行间距 8px，列间距 16px */
  row-gap: 8px;      /* 行间距 */
  column-gap: 16px;  /* 列间距 */
}
```

### 子项属性（设在子元素上）

```css
.item {
  /* 弹性增长：分配剩余空间的比例 */
  flex-grow: 0;   /* 不增长（默认） */
  flex-grow: 1;   /* 等比增长 */

  /* 弹性收缩：空间不足时缩小的比例 */
  flex-shrink: 1;   /* 等比缩小（默认） */
  flex-shrink: 0;   /* 不缩小 */

  /* 基础尺寸 */
  flex-basis: auto;   /* 由内容决定（默认） */
  flex-basis: 200px;  /* 固定 200px */

  /* 简写 */
  flex: 1;            /* = flex-grow:1  flex-shrink:1  flex-basis:0% */
  flex: 0 0 200px;    /* 固定 200px，不增长不缩小 */
  flex: auto;         /* = flex-grow:1  flex-shrink:1  flex-basis:auto */

  /* 单独对齐 */
  align-self: center; /* 覆盖父元素的 align-items */

  /* 排序 */
  order: 0;    /* 默认，值小的在前 */
  order: -1;   /* 排到最前 */
}
```

### 常见 Flex 布局方案

```css
/* 水平垂直居中 */
.center { display: flex; justify-content: center; align-items: center; }

/* 等分布局 */
.equal { display: flex; }
.equal > * { flex: 1; }

/* 侧边栏 + 主内容 */
.sidebar-layout { display: flex; }
.sidebar-layout .sidebar { flex: 0 0 240px; }
.sidebar-layout .main { flex: 1; }

/* 底部固定 */
.sticky-footer { display: flex; flex-direction: column; min-height: 100vh; }
.sticky-footer .content { flex: 1; }
```

---

## 三、Grid 网格布局

### 容器属性

```css
.grid {
  display: grid;

  /* 定义列 */
  grid-template-columns: 200px 1fr 1fr;       /* 3列：固定 + 2等分 */
  grid-template-columns: repeat(3, 1fr);       /* 3等分 */
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));  /* 自适应列数 */

  /* 定义行 */
  grid-template-rows: auto 1fr auto;           /* 头/内容/脚 */

  /* 间距 */
  gap: 16px;

  /* 区域命名 */
  grid-template-areas:
    "header header header"
    "sidebar main   main"
    "footer footer footer";
}
```

### 子项属性

```css
.item {
  grid-column: 1 / 3;          /* 跨第1到第3列 */
  grid-column: span 2;         /* 跨2列 */
  grid-row: 1 / 3;             /* 跨第1到第3行 */
  grid-area: header;           /* 对应 grid-template-areas */
}
```

### 常见 Grid 布局方案

```css
/* 经典圣杯布局 */
.holy-grail {
  display: grid;
  grid-template: auto 1fr auto / 200px 1fr 200px;
  grid-template-areas:
    "header header  header"
    "nav    content aside"
    "footer footer  footer";
  min-height: 100vh;
}
.holy-grail .header  { grid-area: header; }
.holy-grail .nav     { grid-area: nav; }
.holy-grail .content { grid-area: content; }
.holy-grail .aside   { grid-area: aside; }
.holy-grail .footer  { grid-area: footer; }
```

---

## 四、盒模型

```css
.box {
  /* 尺寸 */
  width: 100px;
  height: 100px;
  min-width: 50px;
  max-width: 500px;
  min-height: 50px;
  max-height: 500px;

  /* 内边距 */
  padding: 10px;              /* 四边相同 */
  padding: 10px 20px;         /* 上下 左右 */
  padding: 10px 20px 30px;    /* 上 左右 下 */
  padding: 10px 20px 30px 40px; /* 上 右 下 左（顺时针） */

  /* 外边距 */
  margin: 10px;               /* 同 padding 规则 */
  margin: 0 auto;             /* 水平居中（块级元素） */

  /* 盒模型计算方式 */
  box-sizing: content-box;    /* 默认：width = 内容宽度 */
  box-sizing: border-box;     /* 推荐：width = 内容 + padding + border */

  /* 溢出 */
  overflow: visible;          /* 默认：可见 */
  overflow: hidden;           /* 裁剪 */
  overflow: auto;             /* 内容超出时显示滚动条 */
  overflow: scroll;           /* 始终显示滚动条 */
  overflow-x: hidden;         /* 水平隐藏溢出 */
  overflow-y: auto;           /* 垂直自动滚动条 */
}
```

> ⚡ **全局推荐**：`*, *::before, *::after { box-sizing: border-box; }`

---

## 五、边框与圆角

```css
.border {
  /* 边框 */
  border: 1px solid #ccc;                    /* 简写：宽度 样式 颜色 */
  border-top: 2px dashed red;                /* 单边 */
  border-radius: 8px;                        /* 圆角 */
  border-radius: 50%;                        /* 圆形（正方形元素） */
  border-radius: 8px 8px 0 0;                /* 顶部圆角 */

  /* 边框样式 */
  /* solid | dashed | dotted | double | groove | ridge | inset | outset | none */

  /* 轮廓（不占空间，不影响布局） */
  outline: 2px solid blue;
  outline-offset: 2px;                       /* 轮廓偏移 */
}
```

---

## 六、背景

```css
.bg {
  /* 纯色 */
  background-color: #fff;

  /* 渐变 */
  background: linear-gradient(to right, #ff7e5f, #feb47b);          /* 线性渐变 */
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);   /* 对角渐变 */
  background: radial-gradient(circle, #fff, #000);                  /* 径向渐变 */

  /* 图片 */
  background-image: url("/img/bg.png");
  background-size: cover;            /* 覆盖容器，可能裁剪 */
  background-size: contain;          /* 完整显示，可能留白 */
  background-size: 100% 100%;        /* 拉伸填满 */
  background-repeat: no-repeat;      /* 不重复 */
  background-repeat: repeat-x;       /* 水平重复 */
  background-position: center;       /* 居中 */
  background-position: top right;    /* 右上角 */
  background-attachment: fixed;      /* 滚动时固定 */

  /* 简写 */
  background: #fff url("/img/bg.png") no-repeat center / cover;

  /* 多重背景 */
  background:
    linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)),
    url("/img/bg.png") center / cover;
}
```

---

## 七、文字排版

```css
.text {
  /* 字体 */
  font-family: "Inter", "PingFang SC", "Microsoft YaHei", sans-serif;
  font-size: 16px;
  font-size: 1rem;            /* 相对于根元素字号 */
  font-size: 0.875em;         /* 相对于父元素字号 */
  font-weight: 400;           /* 100-900 */
  font-weight: normal;        /* = 400 */
  font-weight: bold;          /* = 700 */
  font-style: italic;         /* 斜体 */

  /* 颜色 */
  color: #333;
  color: rgb(51, 51, 51);
  color: rgba(51, 51, 51, 0.8);
  color: hsl(0, 0%, 20%);

  /* 行高 */
  line-height: 1.6;           /* 无单位：字号的倍数（推荐） */
  line-height: 24px;          /* 固定值 */

  /* 对齐 */
  text-align: left;           /* 左对齐（默认） */
  text-align: center;         /* 居中 */
  text-align: right;          /* 右对齐 */
  text-align: justify;        /* 两端对齐 */

  /* 装饰 */
  text-decoration: none;                    /* 无装饰 */
  text-decoration: underline;               /* 下划线 */
  text-decoration: line-through;            /* 删除线 */
  text-decoration: underline wavy red;      /* 红色波浪下划线 */

  /* 缩进 */
  text-indent: 2em;           /* 首行缩进 */

  /* 大小写转换 */
  text-transform: uppercase;  /* 全大写 */
  text-transform: lowercase;  /* 全小写 */
  text-transform: capitalize; /* 首字母大写 */

  /* 字间距 */
  letter-spacing: 0.05em;     /* 字符间距 */
  word-spacing: 0.1em;        /* 单词间距 */

  /* 空白处理 */
  white-space: normal;        /* 默认：合并空白，自动换行 */
  white-space: nowrap;        /* 不换行 */
  white-space: pre;           /* 保留空白和换行 */
  white-space: pre-wrap;      /* 保留空白，自动换行 */

  /* 溢出省略 */
  overflow: hidden;
  text-overflow: ellipsis;    /* 单行省略号 */
  white-space: nowrap;        /* 配合使用 */
}

/* 多行省略 */
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;      /* 最多2行 */
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```

---

## 八、颜色与透明度

```css
.color {
  /* 格式 */
  color: #ff0000;                      /* 十六进制 */
  color: #f00;                         /* 缩写 */
  color: rgb(255, 0, 0);               /* RGB */
  color: rgba(255, 0, 0, 0.5);         /* RGBA（半透明） */
  color: hsl(0, 100%, 50%);            /* HSL */
  color: hsla(0, 100%, 50%, 0.5);      /* HSLA */
  color: oklch(0.7 0.15 30);           /* OKLCH（现代色彩空间） */

  /* 透明度 */
  opacity: 0.5;              /* 整个元素半透明 */
  opacity: 0;                /* 完全透明（仍占空间） */
  opacity: 1;                /* 完全不透明 */
}

/* opacity vs rgba 的区别：
   opacity：整个元素（包括子元素）半透明
   rgba：只有指定属性半透明，子元素不受影响 */
```

---

## 九、阴影

```css
.shadow {
  /* 盒子阴影：水平偏移 垂直偏移 模糊 扩展 颜色 */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);                /* 轻微阴影 */
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);               /* 中等阴影 */
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);                /* 重阴影 */
  box-shadow: 0 0 0 1px rgba(0, 0, 0, 0.1);                 /* 模拟边框 */
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);           /* 内阴影 */
  box-shadow: 0 0 0 2px #fff, 0 0 0 4px #667eea;            /* 双圈效果 */

  /* 文字阴影 */
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}
```

---

## 十、变换与过渡

### transform 变换

```css
.transform {
  /* 平移 */
  transform: translateX(20px);         /* 水平移动 */
  transform: translateY(-10px);        /* 垂直移动 */
  transform: translate(20px, -10px);   /* 同时移动 */
  transform: translate(-50%, -50%);    /* 相对自身偏移（常用于居中） */

  /* 缩放 */
  transform: scale(1.1);              /* 放大 10% */
  transform: scale(0.9);              /* 缩小 10% */
  transform: scaleX(2);               /* 水平拉伸 */

  /* 旋转 */
  transform: rotate(45deg);           /* 顺时针 45° */
  transform: rotate(-15deg);          /* 逆时针 15° */

  /* 倾斜 */
  transform: skewX(10deg);

  /* 组合（从右向左依次应用） */
  transform: translate(-50%, -50%) rotate(45deg) scale(1.5);

  /* 变换原点 */
  transform-origin: center;           /* 默认 */
  transform-origin: top left;
  transform-origin: 50% 100%;         /* 底部中心 */
}
```

### transition 过渡

```css
.transition {
  /* 简写：属性 时长 缓动 延迟 */
  transition: all 0.3s ease;
  transition: transform 0.3s ease, opacity 0.2s ease;
  transition: background-color 0.3s ease-in-out 0.1s;

  /* 缓动函数 */
  /* ease          默认：快→慢 */
  /* ease-in       慢→快 */
  /* ease-out      快→慢 */
  /* ease-in-out   慢→快→慢 */
  /* linear        匀速 */
  /* cubic-bezier  自定义贝塞尔曲线 */
}

/* 按钮悬停示例 */
.btn {
  background: #667eea;
  transform: scale(1);
  transition: all 0.2s ease;
}
.btn:hover {
  background: #764ba2;
  transform: scale(1.05);
}
```

### animation 动画

```css
/* 定义关键帧 */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50%      { transform: scale(1.05); }
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.animated {
  /* 简写：名称 时长 缓动 延迟 次数 方向 填充 播放状态 */
  animation: fadeIn 0.5s ease forwards;
  animation: pulse 2s ease-in-out infinite;
  animation: spin 1s linear infinite;

  /* 填充模式 */
  animation-fill-mode: forwards;   /* 结束后保持最终状态 */
  animation-fill-mode: backwards;  /* 开始前应用初始状态 */
  animation-fill-mode: both;

  /* 方向 */
  animation-direction: alternate;  /* 交替正反 */
}
```

---

## 十一、滤镜

```css
.filter {
  filter: blur(4px);                /* 模糊 */
  filter: brightness(1.2);          /* 亮度 */
  filter: contrast(1.5);            /* 对比度 */
  filter: grayscale(100%);          /* 灰度 */
  filter: sepia(100%);              /* 复古色 */
  filter: saturate(2);              /* 饱和度 */
  filter: hue-rotate(90deg);        /* 色相旋转 */
  filter: invert(100%);             /* 反色 */
  filter: drop-shadow(0 4px 6px rgba(0,0,0,0.3));  /* 非矩形阴影 */

  /* 组合 */
  filter: brightness(0.8) contrast(1.2) saturate(1.3);

  /* 背景模糊（毛玻璃） */
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);   /* Safari */
}

/* 毛玻璃效果 */
.glass {
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}
```

---

## 十二、列表与表格

```css
/* 列表 */
ul {
  list-style: none;             /* 去掉圆点 */
  list-style: disc;             /* 实心圆（默认） */
  list-style: decimal;          /* 数字编号 */
  list-style-position: inside;  /* 标记在内部 */
}

/* 表格 */
table {
  border-collapse: collapse;    /* 合并边框（推荐） */
  border-spacing: 0;
  table-layout: fixed;          /* 固定列宽，提升性能 */
  table-layout: auto;           /* 根据内容自动调整（默认） */
}
td, th {
  border: 1px solid #ddd;
  padding: 8px 12px;
  text-align: left;
}
```

---

## 十三、指针与光标

```css
.cursor {
  cursor: pointer;        /* 手型（可点击） */
  cursor: default;        /* 默认箭头 */
  cursor: text;           /* 文本输入 */
  cursor: move;           /* 可移动 */
  cursor: not-allowed;    /* 禁止 */
  cursor: grab;           /* 可抓取 */
  cursor: grabbing;       /* 抓取中 */
  cursor: crosshair;      /* 十字准心 */
  cursor: wait;           /* 等待 */
  cursor: help;           /* 帮助 */
  cursor: none;           /* 隐藏光标 */
}
```

---

## 十四、用户交互

```css
.interaction {
  /* 鼠标事件 */
  pointer-events: none;    /* 忽略所有鼠标事件（穿透） */
  pointer-events: auto;    /* 正常响应（默认） */

  /* 文本选择 */
  user-select: none;       /* 不可选中（按钮、图标） */
  user-select: auto;       /* 默认 */
  user-select: all;        /* 点击全选 */

  /* 拖拽 */
  -webkit-user-drag: none; /* 禁止拖拽图片 */

  /* 滚动 */
  scroll-behavior: smooth; /* 平滑滚动 */
  overscroll-behavior: none; /* 禁止弹性滚动（移动端） */

  /* 触摸 */
  touch-action: none;       /* 禁止触摸手势 */
  touch-action: pan-y;      /* 只允许垂直滚动 */

  /* 焦点样式 */
  :focus-visible {          /* 键盘聚焦时显示，鼠标聚焦不显示 */
    outline: 2px solid #667eea;
    outline-offset: 2px;
  }
}
```

---

## 十五、可见性与显示

```css
.visibility {
  /* display vs visibility vs opacity */
  display: none;        /* 不显示，不占空间，不可交互 */
  visibility: hidden;   /* 不可见，仍占空间，不可交互 */
  opacity: 0;           /* 完全透明，仍占空间，可交互 */

  /* 隐藏但可访问（屏幕阅读器可读） */
  .sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
  }
}
```

---

## 十六、响应式

```css
/* 媒体查询 */
@media (max-width: 768px) {
  .sidebar { display: none; }
  .main { grid-column: 1 / -1; }
}

@media (min-width: 769px) and (max-width: 1024px) {
  .sidebar { width: 200px; }
}

@media (prefers-color-scheme: dark) {
  :root { --bg: #1a1a2e; --text: #e0e0e0; }
}

@media (prefers-reduced-motion: reduce) {
  * { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
}

/* 容器查询（现代 CSS） */
.card-container { container-type: inline-size; }
@container (min-width: 400px) {
  .card { flex-direction: row; }
}

/* 视口单位 */
.full-height { height: 100vh; }       /* 视口高度 */
.full-width  { width: 100vw; }        /* 视口宽度 */
.responsive  { font-size: clamp(14px, 2vw, 18px); }  /* 响应式字号 */
.responsive  { width: min(100% - 2rem, 800px); }      /* 取较小值 */
```

---

## 十七、CSS 变量

```css
/* 定义（通常在 :root） */
:root {
  --color-primary: #667eea;
  --color-secondary: #764ba2;
  --color-bg: #f5f5f5;
  --color-text: #333;
  --spacing-unit: 8px;
  --radius: 8px;
  --shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  --font-sans: "Inter", "PingFang SC", sans-serif;
}

/* 使用 */
.card {
  background: var(--color-bg);
  color: var(--color-text);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  padding: calc(var(--spacing-unit) * 3);
  font-family: var(--font-sans);
}

/* 暗色主题 */
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: #1a1a2e;
    --color-text: #e0e0e0;
  }
}

/* 组件内覆盖 */
.dark-card {
  --color-bg: #2d2d44;
  --color-text: #fff;
}
```

---

## 十八、常用技巧速查

```css
/* 三角形 */
.arrow-up    { width: 0; height: 0; border-left: 5px solid transparent; border-right: 5px solid transparent; border-bottom: 5px solid #333; }
.arrow-down  { width: 0; height: 0; border-left: 5px solid transparent; border-right: 5px solid transparent; border-top: 5px solid #333; }

/* 清除浮动 */
.clearfix::after { content: ""; display: table; clear: both; }

/* 文字不可选中 */
.no-select { user-select: none; }

/* 图片自适应 */
img { max-width: 100%; height: auto; display: block; }

/* 垂直居中（万能方案） */
.center-abs {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* 文字渐变 */
.gradient-text {
  background: linear-gradient(to right, #667eea, #764ba2);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

/* 平滑滚动 */
html { scroll-behavior: smooth; }

/* 禁用输入框 */
input:disabled { opacity: 0.6; cursor: not-allowed; }

/* 长单词/URL换行 */
.break-all { word-break: break-all; }
.break-word { overflow-wrap: break-word; }
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/Scoped-CSS|Scoped CSS]]
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/CSS-Modules|CSS Modules]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Class-and-Style|Class 与 Style 绑定]]
