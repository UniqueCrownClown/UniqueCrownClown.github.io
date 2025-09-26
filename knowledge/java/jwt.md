---
title: jwt
date: 2017-08-21 11:23:51
categories: java #文章分类
tags: [java] #文章标签，可以一次添加多个标签
---

jwt...

<!-- more -->

## token机制
token 比对
token ===》比对===》 用户信息加密得到的 token
acess_token 和 refresh_token 的关系
活跃用户--》token 刷新
非活跃用户--》access_token 过期--》重新登陆
access_token 有效期时长 et
access_token 创建时间到 2*acess_token 失效时间是活跃用户
第一次用账号密码登录服务器会返回两个 token : access_token 和 refresh_token
refresh_token 的有效时间 >= 2 * access_token 的有效时间
refresh_token 就是用来刷新 access_token 。活跃用户的 access_token 过期了，用 refresh_token 获取 新的 access_token
带 access_token 去请求-->过期
-->用 refresh_token 去请求 access_token
-->refresh_token 过期，重置到登陆界面
-->refresh_token 不过期，刷新 access_token
-->不过期  
 每次 刷新 access_token 时判断 refresh_token 是否快过期 [ refresh_token 剩余有效时间 <= 2*access_token 实效]，如果是，那就连 refresh_token 也刷新。
单 token 机制也是符合 oaugh2 的标准的，，只是双 token 机制 aceess_token 时效性短,用 rt 去取 at,,rt 只需过网 2 次，不是每波请求都带，，安全性好一点。。

## Java Spring SSE 实现 (EventSource) event-stream的contentType

```java
@ResponseBody
@RequestMapping(value = "/getDate", produces="text/event-stream;charset=UTF-8")
public void getDate(HttpServletResponse response) throws Exception {
    log.info("getDate event start");
    response.setContentType("text/event-stream");
    response.setCharacterEncoding("UTF-8");
    response.setStatus(200);
    while(!response.getWriter().checkError()){
        response.getWriter().write("data:"+new Date()+"\n\n");
        response.getWriter().flush();
        Thread.sleep(1000);
    }
    response.getWriter().close();
    log.info("getDate event end");
}
```

```js
// 实例化 EventSource 参数是服务端监听的路由
var source = new EventSource("/getDate");
source.onopen = function (event) {
  // 与服务器连接成功回调
  console.log("成功与服务器连接");
};
// 监听从服务器发送来的所有没有指定事件类型的消息(没有event字段的消息)
source.onmessage = function (event) {
  // 监听未命名事件
  console.log("未命名事件", event.data);
};
source.onerror = function (error) {
  // 监听错误
  console.log("错误");
};
// 监听指定类型的事件（可以监听多个）
source.addEventListener("myEve", function (event) {
  console.log("myEve", event.data);
});
```

```js
// 借助 Authorization 查询参数或者 fetch 结合 ReadableStream 来模拟 EventSource 的功能，从而添加请求头
const token = localStorage.getItem('access_token');
const url = 'http://localhost:8080/test/user/getDate';

const headers = {
  // 假设使用 Bearer 认证，具体根据后端要求调整
  Authorization: `Bearer ${token}`,
};

let reader;

// 关闭 SSE 连接的函数
const closeSSEConnection = () => {
  if (reader) {
    reader.cancel();
    console.log('SSE 连接已关闭')
  }
};

fetch(url, { headers })
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    reader = response.body?.getReader();
    if (!reader) {
      throw new Error('No response body');
    }
    const decoder = new TextDecoder();
    let buffer = '';

    const read = () => {
      reader.read().then(({ done, value }) => {
        if (done) {
          return;
        }
        buffer += decoder.decode(value, { stream: true });
        const events = buffer.split('\n\n');
        buffer = events.pop() || '';

        events.forEach((event) => {
          const lines = event.split('\n');
          const eventType = lines
            .find((line) => line.startsWith('event:'))
            ?.split(': ')[1];
          const data = lines.find((line) => line.startsWith('data:'))?.split(': ')[1];

          if (eventType === 'myEve' && data) {
            console.log('myEve', data);
          } else if (!eventType && data) {
            console.log('未命名事件', data);
          }
        });

        read();
      });
    };

    read();
  })
  .catch((error) => {
    console.log('错误', error);
  });

// 示例：在需要关闭连接的地方调用 closeSSEConnection 函数
// setTimeout(closeSSEConnection, 5000); 
```

## 用户认证流程

1. 用户向服务器发送用户名和密码。
2. 服务器验证通过后，在当前对话（session）里面保存相关数据，比如用户角色、登录时间等等。
3. 服务器向用户返回一个 session_id，写入用户的 Cookie。
4. 用户随后的每一次请求，都会通过 Cookie，将 session_id 传回服务器。
5. 服务器收到 session_id，找到前期保存的数据，由此得知用户的身份。

## jwt 和 oAuth2 选型

JWT 是一种认证协议
OAuth2 是一种授权框架，提供了一套详细的授权机制
在讨论 OAuth2 的实现时，会把 JSON Web Token 作为一种认证机制使用
一次性的身份认证、api 的鉴权等，这些场景能充分发挥 jwt 无状态以及分布式验证的优势。
jwt 缺点
Token 有长度限制
Token 不能撤销
需要 token 有失效时间限制(exp)
