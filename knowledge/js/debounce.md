---
title: debounce and throttle
date: 2022-01-05 11:23:51
categories: js
tags: [interview]
---

interview Guide...

<!-- more -->

## 防抖和节流

防抖:触发高频事件后 n 秒内函数只会执行一次，如果 n 秒内高频事件再次被触发，则重新计算时间。
节流:高频事件触发，但在 n 秒内只会执行一次，所以节流会稀释函数的执行频率。

防抖是有条件的周期动作，而节流是没有条件的周期动作。

## 防抖实现

以闭包的方式定义一个全局变量，是实现防抖函数的核心

```js
// 普通版
function debounce(fn, wait) {
  let timeout;
  return function () {
    const context = this,
      args = arguments;
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      fn.apply(context, args);
    }, wait);
  };
}
window.addEventListener(
  "scroll",
  debounce(() => console.log("scroll"), 500)
);
// 立即执行版
function debounce(fn, wait, immediate) {
  let timeout;
  return function () {
    const context = this,
      args = arguments;
    clearTimeout(timeout);
    if (immediate) {
      const callNow = !timeout;
      timeout = setTimeout(function () {
        timeout = null;
      });
      if (callNow) fn.apply(context, args);
    } else {
      timeout = setTimeout(function () {
        fn.apply(context, args);
      }, wait);
    }
  };
}
```

## 节流实现

```js
function throttle(fn, wait) {
  let timeout;
  return function () {
    const context = this,
      args = arguments;
    if (!timeout) {
      timeout = setTimeout(() => {
        timeout = null;
        fn.apply(context, args);
      }, wait);
    }
  };
}
window.addEventListener(
  "scroll",
  throttle(() => console.log("scroll"), 500)
);
// 立即执行版
function throttle(fn, wait, immediate) {
  let timeout;
  let previous = 0;
  return function () {
    let context = this;
    let args = arguments;
    if (immediate) {
      let now = Date.now();
      if (now - previous > wait) {
        fn.apply(context, args);
        previous = now;
      }
    } else {
      if (!timeout) {
        timeout = setTimeout(() => {
          timeout = null;
          fn.apply(context, args);
        }, wait);
      }
    }
  };
}
```
