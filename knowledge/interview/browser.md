---
title: browser
date: 2022-03-01 11:23:51
categories: interview
tags: [browser]
---

http...

<!-- more -->

## 浏览器线程

- GUI 渲染线程
- JS 引擎线程
- 事件触发线程
- 定时器触发线程
- HTTP 请求线程

## 同步与异步执行顺序

1. js 将任务分为同步任务和异步任务，同步任务进入主线中，异步任务首先到 Event Table 进行回调函数注册
2. 当异步任务的触发条件满足，将回调函数从 Event Table 压入 Event Queue 中
3. 主线程的同步任务执行完毕，系统从 Event Queue 中读取异步的回调函数
4. 主线程空了去 Event Queue 读取回调函数的过程称为 Event Loop
5. 异步任务进行了更精细的划分，，异步任务又进一步分为微任务和宏任务
   <!-- ![任务分类视图](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed@master/hexo/knowledge/nodeJsPic/task.png) -->
   [![XpBRu6.md.png](https://s1.ax1x.com/2022/05/23/XpBRu6.md.png)](https://imgtu.com/i/XpBRu6)
6. 宏任务和微任务分别有各自的任务队列 Event Queue，即宏任务队列和微任务队列。
7. Promise 中的函数是立即执行的，而后续的 .then 则会被分发到 microtask 的 Promise 队列中去
8. 宏任务和微任务，本质上也是让 JS 脚本能够在渲染阶段前后，有了完成某些功能的能力。
[JavaScript为什么要有微任务？](https://juejin.cn/post/7086307441847042062#heading-2)

## 宏任务与微任务执行顺序

1. 代码开始执行,创建一个全局调用栈,script 作为宏任务执行
2. 执行过程同步任务立即执行,异步任务根据异步任务类型分别注册到微任务队列和宏任务队列
3. 同步任务执行完毕，查看微任务队列

- 若存在微任务，将微任务队列全部执行(包括执行微任务过程中产生的新微任务)
- 若无微任务，查看宏任务队列，执行第一个宏任务，宏任务执行完毕，查看微任务队列，重复上述操作，直至宏任务队列为空
  Event Loop 异步任务执行顺序图:
    <!-- ![异步任务执行顺序](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed@master/hexo/knowledge/nodeJsPic/taskProcess.png) -->

  [![XpBWDK.png](https://s1.ax1x.com/2022/05/23/XpBWDK.png)](https://imgtu.com/i/XpBWDK)

- microtasks
  - process.nextTick
  - promise
  - Object.observe (废弃)
  - MutationObserver
- macrotasks
  - setTimeout
  - setImmerdiate
  - setInterval
  - I/O
  - UI 渲染
- 代码执行时序分析:
  created 同步
  mounted 同步
  promise.then 异步微
  setTimeout 异步宏

先同步再异步，异步先微再宏

```js
created(){
    console.log("created");
    new Promise(resolve => {
        resolve()
    }).then( res => {
        console.log(".then");
        setTimeout(()=>{
            console.log("setTimeout  -- 3000")
        },3000);
    });
    setTimeout(() => {
        console.log("setTimeout -- 5000");
    },5000);
},
mounted() {
    console.log("mounted")；
}

// 结果:
// created
// mounted
// .then
// setTimeout -- 3000
// setTimeout -- 5000
```

## 浏览器渲染

浏览器可以对拿到手的资源进行缓存，避免对一个资源的重复请求获取，节约时间提升性能

假如现在页面里放在 head 的 css 文件下载速度很慢，页面会出现什么情况？
大概页面会等待这个 CSS 的下载，这个时候页面是白屏状态，然后这个 CSS 资源会有一个超时时间，
假如超过了这个超时时间，这个资源相当于会下载失败，浏览器会直接跳过这个 CSS 资源，
根据已有的 CSS 资源生成 CSS 规则树，进而生成 Render 树，然后渲染页面。

## 浏览器对同一域名进行请求的最大并发连接数

ie10,11,Chrome 和 Firefox 限制数为 **6** 个

ajax 请求是在第 6 个请求的时候就开始有 Stalled 时间了,当有 6 个响应时间比较长的 ajax 请求的时候会阻塞到后面的 ajax 请求执行，少于 6 个的时候不会阻塞后续的 ajax 执行。

## 浏览器缓存

1. 强缓存：上一次的 HTTP 交互中，服务器通知了浏览器一个缓存时间，在该时间内对该资源的请求都可以直接使用缓存。
2. 协商缓存：向服务器发送请求，服务器会根据这个请求的 request header 的参数来判断是否命中，如果命中则返回 304 状态码告诉浏览器可以直接使用缓存，否则就重新处理请求并返回资源。

判断命中与否主要是通过对 **request header** 中的 **If-Modified-Since** 和 **Etag** 或**If-None-Match**字段与服务器上资源的信息进行比对分析得出的。

3. 浏览器先根据这个资源的 http 头信息来判断是否命中强缓存。如果命中则直接加在缓存中的资源，并不会将请求发送到服务器。

4. 如果未命中强缓存，则浏览器会将资源加载请求发送到服务器。服务器来判断浏览器本地缓存是否失效。若可以使用，则服务器并不会返回资源信息，浏览器继续从缓存加载资源。

5. 如果未命中协商缓存，则服务器会将完整的资源返回给浏览器，浏览器加载新资源，并更新缓存。

### http 服务器代理缓存

让代理服务器接管一部分的服务端 HTTP 缓存，客户端缓存过期后就近到代理缓存中获取，代理缓存过期了才请求源服务器，这样流量巨大的时候能明显降低源服务器的压力。

缓存代理的控制分为两部分，一部分是源服务器端的控制，一部分是客户端的控制。

1. 源服务器控制

cache-Control:public/private,表示是否允许代理服务器缓存，前者禁止，后者为允许

must-revalidate 的意思是客户端缓存过期就去源服务器获取，而 proxy-revalidate 则表示代理服务器的缓存过期后到源服务器获取。

s-maxage 限定了缓存在代理服务器中可以存放多久

2. 客户端控制

max-stale 对代理服务器上的缓存进行宽容操作,客户端到代理服务器上拿缓存的时候，即使代理缓存过期了也不要紧，只要过期时间在 5 秒之内，还是可以从代理中获取的

min-fresh 对代理服务器上的缓存进行限制操作,代理缓存需要一定的新鲜度，不要等到缓存刚好到期再拿，一定要在到期前 5 秒之前的时间拿，否则拿不到。

only-if-cached 这个字段加上后表示客户端只会接受代理缓存，而不会接受源服务器的响应。如果代理缓存无效，则直接返回 504（Gateway Timeout）。

### request 的状态码说明

304 接受请求，响应浏览器继续用缓存(协商缓存)
200 接受请求,响应新的资源，生成新的浏览器缓存
[资源](https://www.cnblogs.com/ranyonsue/p/8918908.html)

### HTTP 缓存 Cache-Control 响应头

1. Cache-Control:max-age=N
   浏览器获取到资源内容后，将资源内容缓存在本地，缓存有效期是 N 秒。
   若过期前再次访问资源，直接使用本地缓存；过期后再访问，则向服务器发请求，若服务器检查资源没有更新，则返回 304 状态码；如果有更新，则返回 200 状态码以及新的资源内容。同时浏览器延长本地资源的缓存有效期。
2. Cache-Control:no-cache
   资源可以缓存在浏览器本地，但每次使用缓存前，都要跟服务器协商确认资源是否有更新。
   和 1 的区别是比 1 多了个不经过服务器的有效期
3. Cache-Control:no-store
   禁用缓存。浏览器每次都要请求服务器，服务器每次都要返回 200 状态码以及资源内容。
4. Cache-Control:max-age=N,must-revalidate
   和 1 一致,在没加 must-revalidate 的情况下，如果服务器发生异常没有响应浏览器的请求，则浏览器会使用本地已过期的缓存；如果有加，则不会使用过期的缓存

## xxs 攻击和 csrf/xsrf 攻击

通常来说 CSRF(点击或者是携带敏感 cookies 访问三方服务器，三方服务器向目标服务器发起权限之下的请求)
是由 XSS(代码注入)实现的==>jwt 实现防御

X-XSS-Protection 这个响应头是用来防范 XSS 的。最早我是在介绍 IE8 的文章里看到这个，现在主流浏览器都支持，并且默认都开启了 XSS 保护

1. 输入过滤
   对用户的输入进行过滤，包括 text、post 等等所有输入进行可靠性检测。
   也可以制作一个白名单，一但输入中的字符不在白名单内，自动过滤掉。
2. 转义
   与正则的思想类似，对比如<、>等等特殊字符进行转义处理。

decodingMap， 用于对用户输入所包含的特殊字符或标签进行编码或过滤，如 <，>，script，防止 XSS 攻击
验证码会强制用户必须与应用进行交互，才能完成最终请求。因为通常情况下，验证码能够很好地遏制 CSRF 攻击

SameSite=Strict，一般就是自己带上 referer

目前防御 CSRF 攻击主要有三种策略：

1. 验证 HTTP Referer 字段；
2. 在请求地址中添加 token 并验证；
3. 在 HTTP 头中自定义属性并验证。（都需要在后台验证）

token 可以在用户登陆后产生并放于 session 之中，然后在每次请求时把 token 从 session 中拿出，与请求中的 token 进行比对

## 若依的xss注入实现

xss注解实现的例子

不给插入xml格式的内容

实现ConstraintValidator的isValid





## 跨域

跨域三大宝：jsonp(原理是 script 的 src 标签可以跨域，所以数据交互都封成 json 格式)，cors(原理是在服务端的 response 的 header 上直接写个 Access-Control-Allow-Origin:"\*"就能过去了)nginx 的代理(反向代理修改配置文件，前提服务器是 nginx)

```
location /promotion/activityPro {
            proxy_pass http://frontHost/promotion/activityPro;
            add_header 'Access-Control-Allow-Origin' '*';
}
```

nginx,tomcat,apache,IIS

### jsonp cors 和 ajax

在未正确设置的情况下，跨域访问会提示如下错误：

No 'Access-Control-Allow-Origin' header is present on the requested resource.
jsonp 只支持 get 方式（因为本质是通过 script 标签的 src 属性访问 url）
ajax 直接请求普通文件存在跨域无权限访问的问题
web 页面调用 js 文件不受跨域的影响(凡是含有"src"这个属性的标签都拥有跨域的能力)
想通过纯 web 端跨域的方法:
在远程服务器上把数据装进 js 格式的文件里，供客户端调用和进一步处理
jsonp 和 ajax 的区别和应用
相同
请求一个 url，然后把服务器返回的数据进行处理
不同
ajax 的核心是通过 XmlHttpRequest 获取非本页内容
jsonp 的核心则是动态添加 script 标签来调用服务器提供的 js 脚本
ajax 通过服务端代理一样可以实现跨域，jsonp 本身也不排斥同域的数据的获取。
应用
可以直接在\$.ajax({})中定义 dataType: "jsonp"来实现跨域

### Cross-Origin Resource Sharing (CORS)

通过设置 HTTP 请求和返回中 header，告知浏览器该请求是合法的
涉及到服务器端和浏览器端双方的设置：请求的发起(Http Request Header)和服务器对请求正确的响应（Http response header）

原生的 xmlrequest 对象如何实现跨域

```js
// JQuery的$.ajax()可以用来发起XHR或者CORS请求,,提供的跨域方法
$.ajax({
  // The 'type' property sets the HTTP method.
  // A value of 'PUT' or 'DELETE' will trigger a preflight request.
  type: "GET",

  // The URL to make the request to.
  url: "http://updates.html5rocks.com",

  // The 'contentType' property sets the 'Content-Type' header.
  // The JQuery default for this property is
  // 'application/x-www-form-urlencoded; charset=UTF-8', which does not trigger
  // a preflight. If you set this value to anything other than
  // application/x-www-form-urlencoded, multipart/form-data, or text/plain,
  // you will trigger a preflight request.
  contentType: "text/plain",

  xhrFields: {
    // The 'xhrFields' property sets additional fields on the XMLHttpRequest.
    // This can be used to set the 'withCredentials' property.
    // Set the value to 'true' if you'd like to pass cookies to the server.
    // If this is enabled, your server must respond with the header
    // 'Access-Control-Allow-Credentials: true'.
    withCredentials: false,
  },

  headers: {
    // Set any custom headers here.
    // If you set any non-simple headers, your server must include these
    // headers in the 'Access-Control-Allow-Headers' response header.
  },

  success: function () {
    // Here's where you handle a successful response.
  },

  error: function () {
    // Here's where you handle an error response.
    // Note that if the error was due to a CORS issue,
    // this function will still fire, but there won't be any additional
    // information about the error.
  },
});
```

服务器正确响应 CORS 请求
1、简单请求(重点)
get,post,head
header 内容= Accept+Accept Language+Content Language+Last Event ID+Content Type：仅接受 application/x-www-form-urlencoded，multipart/form-data，text/plain
栗子
b.com 向 a.com 发起跨域请求

怎么起两个服务器做实验呢??

```js
// 客户端js：

var xhr = XMLHttpRequest();
xhr.open("post", url, true); //url绝对路径，true异步，false同步
xhr.withCredentails = true;
xhr.onload = function () {};
xhr.send();

// 服务端(node):

res.setHeader("Access-Control-Allow-Origin", req.header.origin);
res.setHeader("Access-Control-Allow-Credentails", true); //告诉客户端可以在http请求中加上cookie
res.setHeader("Access-Control-Allow-Method", "POST,GET,PUT,DELETE,OPTIONS");
```

cors是前后端需要配合使用，后端配置，，前端的请求发送也要带上相应的跨域标志位
```js
fetch("http://10.8.6.125/auth-center/login?username=admin123&password=12345678", {
        mode: "cors",
        method: 'post',
})
```

### nginx配置跨域

$request_method = 'OPTIONS',注意options请求的配置

```
add_header 'Access-Control-Allow-Origin' '*';
add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS';
add_header 'Access-Control-Allow-Headers' 'x-requested-with, Authorization, Content-Type';
if ($request_method = 'OPTIONS') {
    add_header 'Access-Control-Allow-Origin' '*';
    add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS';
    add_header 'Access-Control-Allow-Headers' 'Authorization, Content-Type';
    add_header 'Access-Control-Allow-Credentials' 'true';
    add_header 'Access-Control-Max-Age' 1728000;
    add_header 'Content-Length' 0;
    return 204;
}
```

## 主流鉴权方案

1. 引入 Redis 做分布式会话，即用户登录成功后，将用户身份、权限信息存入 Redis，以一个唯一 ID 作为 Key，并设置信息在 Redis 里的失效时间。这个唯一 ID 的 Key 将返回给客户端，客户端可以放入 Cookie，sessionStorage 等处做本地存储。下次访问的时候，将这个唯一 ID 放入请求参数中一起发送（一般放入 Header）。服务端通过检查 Redis 里有无这个 ID 来判断用户是否登录，获取用户身份和权限信息。客户端如果长时间没有操作，则存储在 Redis 里会话信息过期自动删除。客户端每访问一次服务端，需刷新一次会话信息的过期时间，避免固定过期时间带来的低用户体验。
2. JWT，即 Java Web Token。用户登录成功后，服务端向客户端返回的唯一 ID 不再是无意义的字符串，而是包含了用户身份、权限、失效时间等信息的加密字符串。并且这个字符串包含数字签名，服务端可对这个字符串做数字签名验签，确保该字符串未经篡改和伪造。相比分布式会话方案，JWT 虽省去了 Redis 存储，但是每次访问都要做数字签名验证，增加了 CPU 的资源损耗。

## token

### 说明

json web token 简称 JWT，是基于 JSON 的一种开放标准
JWT = base64 编码(header) + '.' + base64 编码(payload) + '.' + signature

### token 流程

1. 用户使用用户名密码来请求服务器
2. 服务器进行验证用户的信息
3. 服务器通过验证发送给用户一个 token
4. 客户端存储 token，并在每次请求时附送上这个 token 值
5. 服务端验证 token 值，并返回数据

```js
// 客户端应用
fetch("api/user/1", {
  headers: {
    Authorization: "Bearer " + token,
  },
});
```

### Token 和 JWT 的区别

- Token：服务端验证客户端发送过来的 Token 时，还需要查询数据库获取用户信息，然后验证 Token 是否有效(Session 的用法)。
- JWT： 将 Token 和 Payload 加密后存储于客户端，服务端只需要使用密钥解密进行校验（校验也是 JWT 自己实现的）即可，不需要查询或者减少查询数据库，因为 JWT 自包含了用户信息和加密的数据
- Cookies 和 WebStorage,web storage 是用于本地大容量存储数据(web storage 的存储量大到 **5MB**);而 cookie 是用于客户端和服务端间的信息传递；Cookies 大小限制为 **4kb**,cookie 既可以由服务端来设置，也可以由客户端来设置。
- 当客户端要发送 http 请求时，浏览器会先检查下是否有对应的 cookie。有的话，则**自动**地添加在 request header 中的 cookie 字段。注意，每一次的 http 请求时，如果有 cookie，浏览器都会自动带上 cookie 发送给服务端。那么把什么数据放到 cookie 中就很重要了，因为很多数据并不是每次请求都需要发给服务端，毕竟会增加网络开销，浪费带宽。
- 服务端可以设置 cookie 的所有选项：expires、domain、path、secure、HttpOnly
- 在客户端是不能通过 js 代码去设置一个 httpOnly 类型的 cookie 的，这种类型的 cookie 只能通过服务端来设置。

### 单点登录

身份验证服务



### JWT 基本

json web token 简称 JWT，是基于 JSON 的一种开放标准
JWT = base64 编码(header) + '.' + base64 编码(payload) + '.' + signature
单点登录是当今广泛使用 JWT 的一项功能，因为它的开销很小。

token 流程

- 需要一个 secret（随机数）
- 后端利用 secret 和加密算法(如：HMAC-SHA256)对 payload(如账号密码)生成一个字符串(token)，返回前端
- 前端每次 request 在 header 中带上 token
- 后端用同样的算法解密

1. 用户使用用户名密码来请求服务器
2. 服务器进行验证用户的信息
3. 服务器通过验证发送给用户一个 token
4. 客户端存储 token，并在每次请求时附送上这个 token 值
5. 服务端验证 token 值，并返回数据
6. 客户端应用

```js
fetch("api/user/1", {
  headers: {
    Authorization: "Bearer " + token,
  },
});
```

### JSON Web Token 和 Session Cookies 的对比

用来处理在不同页面之间切换，保存用户登录信息的机制。

1. Session Cookies 也称为会话 Cookies，在 Session Cookies 中，用户的登录状态会保存在服务器的内存中。当用户登录时，Session 就被服务端安全的创建。

在每次请求时，服务器都会从会话 Cookie 中读取 SessionId，如果服务端的数据和读取的 SessionId 相同，那么服务器就会发送响应给浏览器，允许用户登录。

2. Session Cookies 是存储在服务器内存中，这就意味着如果网站或者应用很大的情况下会耗费大量的资源。由于 JWT 是无状态的，在许多情况下，它们可以节省服务器资源。因此 JWT 要比 Session Cookies 具有更强的可扩展性。

3. JWT 具有加密签名，而 Session Cookies 则没有。JWT 是无状态的，因为声明被存储在客户端，而不是服务端内存中。Session Cookies 是存储在服务器内存中，这就意味着如果网站或者应用很大的情况下会耗费大量的资源。由于 JWT 是无状态的，在许多情况下，它们可以节省服务器资源。因此 JWT 要比 Session Cookies 具有更强的可扩展性。

4. Session Cookies 只能用在单个节点的域或者它的子域中有效。JWT 能够通过多个节点进行用户认证，也就是我们常说的跨域认证。

5. 用计算时间(JWT)换存储空间(Session Cookies)

6. 同域内浏览器中发出的任何一个请求都会带上 cookie，无论请求什么资源，请求时，cookie 会出现在请求头的 cookie 字段中。

### 无感刷新 token

token 过期时间太短，需要频繁的去登录
后端提供刷新 token 的接口，实现无感刷新的思路

1. 定时器调接口,定时刷新 token 接口
2. 响应拦截器中拦截，判断 token 返回过期后，调用刷新 token 接口
   token 定期过期，这时请求返回 token 过期，就需要拿着 refreshToken 去请求新的 accessToken，
   但是因为 ajax 是异步请求，所以会存在多个接口重复刷新 token，引发报错。解决办法：刷新一次 token，其余请求拦截放入缓存中，等 token 刷新成功后在发起请求。(资料示例)[https://www.jianshu.com/p/2a3cae40fc7f]

### readme

1. 服务器可以在响应头中设置 Cookie 的过期时间。通常情况下，Session Cookies 是会话的临时存储，当用户关闭浏览器或超时后，Cookie 会被删除。
