---
title: csstrick
date: 2019-03-05 11:20:32
categories: frontend #文章分类
tags: [csstrick] #文章标签，可以一次添加多个标签
---

csstrick...

<!-- more -->

## csstrick

### simple experience

```css
/* 文本高亮 */
&:hover {
  color: #fff;
  text-shadow: -1px 1px 5px #08c, 1px -1px 5px #08c;
}
```

### BFC

BFC 是 CSS 布局的一个概念，是一块独立的渲染区域，是一个环境，里面的元素不会影响到外部的元素 。

如何生成 BFC：（即脱离文档流）

1. 根元素，即 HTML 元素（最大的一个 BFC）
2. float 的值不为 none
3. position 的值为 absolute 或 fixed
4. overflow 的值不为 visible（默认值。内容不会被修剪，会呈现在元素框之外）
5. display 的值为 inline-block、table-cell、table-caption
   BFC 布局规则：

6. 内部的 Box 会在垂直方向，一个接一个地放置。

7. 属于同一个 BFC 的两个相邻的 Box 的 margin 会发生重叠

8. BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此, 文字环绕效果，设置 float

9. BFC 的区域不会与 float box 重叠。

10. 计算 BFC 的高度，浮动元素也参与计算

BFC 作用：

1. 自适应两栏布局

2. 可以阻止元素被浮动元素覆盖

3. 可以包含浮动元素---清除内部浮动 原理：触发父 div 的 BFC 属性，使下面的子 div 都处在父 div 的同一个 BFC 区域之内

4. 分属于不同的 BFC 时，可以阻止 margin 重叠

### Tailwind CSS

Tailwind CSS 是一套以 Atomic/Utility-First CSS(我们一般用基于 BEM 的 Semantic CSS) 为基础的完整的设计系统（Design System）。对于喜欢「CSS in JS」写法的开发人员，可以借助 twin.macro 这个库，在「CSS in JS」中引用 Tailwind CSS。这样就可以既享受「CSS in JS」的优势，又能保证 Tailwind CSS 作为样式层的 Single Source Of Truth。

### Windi CSS

相比于 Tailwind CSS 具有按需加载，零依赖等特性。
(Windi Css)[https://windicss.org/]

### unoCss

(unoCss)[https://github.com/unocss/unocss]

### 换肤

1.  使用 js 去修改 link 的 href

```html
<link id="link_theme" href="skin.css" rel="stylesheet" type="text/css" />
<script>
  document.getElementById("link_theme").href = "skin-dark.css";
</script>
```

2.  Element-UI 的换肤实现

先把样式中颜色全部替换后再塞到\<style\>标签里面,使用 less 的 modifyVars 动态修改
modifyVars 方法是是基于 less 在浏览器中的编译来实现。所以在引入 less 文件的时候需要通过 link 方式引入，然后基于 less.js 中的方法来进行修改变量

```html
// styles.less @color: red; .card { color: @color; }
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <link rel="stylesheet/less" type="text/css" href="styles.less" />
  <script src="//cdn.jsdelivr.net/npm/less"></script>
</head>

<body>
  <div class="card">card</div>
  <button id="themeBtn">更改颜色</button>
</body>
<script>
  document.getElementById("themeBtn").addEventListener("click", function () {
    let color =
      "#" +
      Math.floor(Math.random() * 256 * 1000000000)
        .toString(16)
        .slice(0, 6);
    less
      .modifyVars({
        "@color": color,
      })
      .then(() => {
        console.log(`color: ${color}F`);
      });
  });
</script>
```

3. css 原生变量

   **root.style.setProperty**

```html
<style>
  // 加上前缀 -- 就可以了
  :root {
    --color: red;
  }
  //使用 当--color 不生效的时候会使用后面参数替代
  body: {
    color: var(--color, #000);
  }
</style>
<script>
  // 使用 js 去修改

  // 获取根
  let root = document.documentElement;
  root.style.setProperty("--color", "#f00");
</script>
```

### 黑白配色

```htmlzhen
<style>
  html {
    filter: grayscale(1);
  }
</style>
```

### requestAnimationFrame

与 setTimeout 相比，requestAnimationFrame 最大的优势是由系统来决定回调函数的执行时机。具体一点讲，如果屏幕刷新率是 60Hz,那么回调函数就每 16.7ms 被执行一次，如果刷新率是 75Hz，那么这个时间间隔就变成了 1000/75=13.3ms，换句话说就是，requestAnimationFrame 的步伐跟着系统的刷新步伐走。它能保证**回调函数在屏幕每一次的刷新间隔中只被执行一次**，这样就不会引起丢帧现象，也不会导致动画出现卡顿的问题。

requestAnimationFrame 则完全不同，当页面处理未激活的状态下，该页面的屏幕刷新任务也会被系统暂停，因此跟着系统步伐走的 requestAnimationFrame 也会停止渲染，当页面被激活时，动画就从上次停留的地方继续执行，有效节省了 CPU 开销。

一个刷新间隔内函数执行多次时没有意义的，因为显示器每 16.7ms 刷新一次，多次绘制并不会在屏幕上体现出来。

```js
const element = document.getElementById("some-element-you-want-to-animate");
let start, previousTimeStamp;
let done = false;

function step(timestamp) {
  if (start === undefined) {
    start = timestamp;
  }
  const elapsed = timestamp - start;

  if (previousTimeStamp !== timestamp) {
    // 这里使用 `Math.min()` 确保元素刚好停在 200px 的位置。
    const count = Math.min(0.1 * elapsed, 200);
    element.style.transform = "translateX(" + count + "px)";
    if (count === 200) done = true;
  }

  if (elapsed < 2000) {
    // 在两秒后停止动画
    previousTimeStamp = timestamp;
    if (!done) {
      window.requestAnimationFrame(step);
    }
  }
}

window.requestAnimationFrame(step);
```

### 手绘 css 库

(DoodleCSS)[https://github.com/chr15m/DoodleCSS]

### cyberpunk-inspired webui

(augmented-ui)[https://github.com/propjockey/augmented-ui]

### vaporwave

(aesthetic-css)[https://github.com/torch2424/aesthetic-css]
