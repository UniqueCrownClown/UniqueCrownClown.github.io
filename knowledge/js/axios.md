---
title: fetch和axios
date: 2022-01-02 11:23:51
categories: js
tags: [interview]
---

fetch 和 axios...

<!-- more -->

### fetch

fetch 新语法是没有 catch()捕捉报错返回的，所以当我们发起一个不存在的请求 url 时，依旧会走 then，因为 fetch 里面没有 catch 这条路。

```js
fetch("/json?user=覃")
  .then((res) => {
    return res.text();
  })
  .then((data) => {
    console.log(data);
  });
fetch("/goods", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ user: "张伟" }),
})
  .then((res) => {
    return res.json();
  })
  .then((data) => {
    console.log(data);
  });
```

nodejs v17.5.0 开始支持 fetch API

### axios

axios.js-->core/Axios.js-->core/InterceptorManager.js-->handles.push
-->Axios.js 的 request-->dispatchRequest 调用 adapter return promise -->requestInterceptorChain + [dispatchRequest.bind(this), undefined] + responseInterceptorChain-->promise.then 递归执行

interceptor.synchronous: 是否同步执行拦截器,通过前一步的 Promise 响应构建 Promise 响应链并执行,其实就是 config 的是否需要传递,默认异步

options.runWhen: 用于运行时检查执行特定的拦截器，可以添加一个 runWhen 函数到 options，当函数返回 false 时该拦截器不会执行。

```js
let promise;
promise = new Promise((resolve, reject) => {
  resolve("橙某人");
});
promise = promise.then((r1) => {
  return r1;
});
promise = promise.then((r2) => {
  return r2;
});
promise = promise.then((res) => {
  console.log(res); // 橙某人
});
```

### 取消重复请求的实现

XMLHttpRequest 对象的 abort( )方法 来取消正在进行的 HTTP 请求

### axios 取消请求的实现

```js
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios
  .get("/user/12345", {
    cancelToken: source.token,
  })
  .catch(function (thrown) {
    if (axios.isCancel(thrown)) {
      console.log("Request canceled", thrown.message);
    } else {
      // 处理错误
    }
  });

// 取消请求（message 参数是可选的）
source.cancel("Operation canceled by the user.");
```

### axios 是怎么抵御 XSRF

防 xsrf 的最有效方式就是，在请求头中添加自定义属性，属性值为后台返回的 token（一般存放在 cookie 中），当后台服务器接受收到浏览器的请求之后，会拿到请求头中的 token，进行对比

```js
//`xsrfCookieName`是要用作 xsrf 令牌的值的cookie的名称
xsrfCookieName: 'XSRF-TOKEN', // default

// `xsrfHeaderName`是携带xsrf令牌值的http头的名称
xsrfHeaderName: 'X-XSRF-TOKEN', // default
```

### fetch 和 xmlRequest 的区别

fetch 没有中断机制,没有超时,没有进度功能

AbortController
