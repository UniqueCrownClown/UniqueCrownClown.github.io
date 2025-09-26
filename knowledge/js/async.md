---
title: js异步
date: 2017-12-21 11:23:51
categories: js #文章分类
tags: [js异步] #文章标签，可以一次添加多个标签
---

js 异步...

<!-- more -->

## eventLoop

Event Loop 执行顺序图:

<!-- ![任务执行顺序](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed@master/hexo/knowledge/nodeJsPic/eventLoop.png) -->

[![XpBgjx.md.png](https://s1.ax1x.com/2022/05/23/XpBgjx.md.png)](https://imgtu.com/i/XpBgjx)

## 异步代码常见规范

1. reject 一个 Error 对象

```js
// bad
Promise.reject("error reason");

// good
Promise.reject(new Error("error reason"));
```

2. 不要在 Promise 上使用 async

不要把 async 函数传递给 Promise 构造函数，因为没有必要，其次如果 async 函数异常，那么你的 promise 并不会 reject

```js
// bad
new Promise(async (resolve, reject) => {})

// good
new Promise((resolve, reject) => { async function(){coding....}()})

```

3. 不要使用 await 在循环中

尽可能将这写异步的任务改为并发，可以大幅提高代码执行效率

```js
// bad
for (const apiPath of paths) {
  const { data } = await request(apiPath);
}

// good
const results = [];
for (const apiPath of paths) {
  const res = resquest(apiPath);
  results.push(res);
}
await Promise.all(results);
```

4. **不要再 Promise 中使用 return 语句**

Promise 构造函数不希望回调返回任何形式的值

**return 一般用来控制该函数中的执行流程**

```js
// 一般来说，调用resolve或reject以后，Promise 的使命就完成了，后继操作应该放到then方法里面
new Promise((resolve, reject) => {
  return resolve(1);
  console.log("2"); // 不会执行
});
```

```js
// bad
new Promise((resolve, reject) => {
  if (isOK) return "ok";
  return "not ok";
});
// good
new Promise((resolve, reject) => {
  if (isOK) resolve("ok");
  reject(new Error("not ok"));
});
```

5. 别忘了异常处理

- Promise

```js
// bad
asyncPromise().then(() => {});

// good
asyncPromise()
  .then(() => {})
  .catch(() => {});
```

- async await

```js
// bad
const result = await asyncPromise()

// good
try {
    const result = await asyncPrmise()
} catch() {
    // do something
}

```
