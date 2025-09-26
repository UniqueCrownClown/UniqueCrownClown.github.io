---
title: js笔记
date: 2017-10-21 11:23:51
categories: js #文章分类
tags: [js] #文章标签，可以一次添加多个标签
---

js...

<!-- more -->

### 严格模式

ES5 引入了严格模式（strict mode）的概念。
严格模式是一种不同的 JavaScript 执行模式，它提供了不同的变量解析和错误处理规则。
要在脚本中启用严格模式，需要在脚本顶部添加 `"use strict";` 指令。
严格模式的目的是消除 JavaScript 语法的一些不合理、不严谨之处，减少一些怪异行为。
消除代码运行的一些不安全之处，保证代码运行的安全；
提高编译器效率，增加运行速度；
为未来新版本的 JavaScript 做好铺垫。
例如：
1. 严格模式下，变量必须先声明，再使用。
2. 严格模式下，函数的 this 指向 undefined，而不是 window。
3. 严格模式下，不能删除变量或函数。
4. 严格模式下，arguments 不会自动反映函数参数的变化。
5. 严格模式下，不能使用 with 语句。
6. 严格模式下，创建 eval 作用域，变量只能在 eval 中声明，外部不能引用。


### 深拷贝和浅拷贝

1. 浅拷贝:将原对象或原数组的引用直接赋给新对象，新数组，新对象／数组只是原对象的一个引用
2. 深拷贝:创建一个新的对象和数组，将原对象的各项属性的“值”（数组的所有元素）拷贝过来，是“值”而不是“引用”

- Object.assign({},object)
- es6 对象扩展符
- 存在大量深拷贝需求的代码——immutable 提供的解决方案

### 前端路由的实现原理

本质上是监测浏览器地址栏 url 的变化，截取 url 地址，然后根据相应的规则做不同的处理

### 原生 js 监听路由变化

window.history 指向 History 对象，它表示当前窗口的浏览历史。
History 对象保存了当前窗口访问过的所有页面网址。
history.popstate 只能监听 back/forward/go 却不能监听 history.pushState，所以需要额外全局复写一下 history.pushState 事件。
js 可以由此和 window 的 dispach 来监听路由状态
原生发布订阅模式-->自定义事件的触发 dispatchEvent # document.createEvent() # event.initEvent() # element.dispatchEvent()
自定义事件的函数有 Event、CustomEvent 和 dispatchEvent
// 向 window 派发一个 resize 内置事件
window.dispatchEvent(new Event('resize'))

### 长短连接和长短轮询

客户端和服务端的 Connection 设置为 keep-alive 时
HTTP 协议是基于请求/响应模式的，因此只要服务端给了响应，本次 HTTP 连接就结束了
TCP 连接是一个双向的通道，它是可以保持一段时间不关闭的，因此 TCP 连接才有真正的长连接和短连接这一说
HTTP1.0 协议不支持长连接，从 HTTP1.1 协议以后，连接默认都是长连接，也就是默认 Connection 的值就是 keep-alive
长连接情况下，多个 HTTP 请求可以复用同一个 TCP 连接，这就节省了很多 TCP 连接建立和断开的消耗。
长连接并不是永久连接的。如果一段时间内（具体的时间长短，是可以在 header 当中进行设置的，也就是所谓的超时时间），这个连接没有 HTTP 请求发出的话，那么这个长连接就会被断掉。
在长轮询中，服务器如果检测到库存量没有变化的话，将会把当前请求挂起一段时间（这个时间也叫作超时时间，一般是几十秒）。在这个时间里，服务器会去检测库存量有没有变化，检测到变化就立即返回，否则就一直等到超时为止。
对于客户端来说，不管是长轮询还是短轮询，客户端的动作都是一样的，就是不停的去请求，不同的是服务端，短轮询情况下服务端每次请求不管有没有变化都会立即返回结果，而长轮询情况下，如果有变化才会立即返回结果，而没有变化的话，则不会再立即给客户端返回结果，直到超时为止。

### 长轮询和长短连接的区别

第一个区别是决定的方式，一个 TCP 连接是否为长连接，是通过设置 HTTP 的 Connection Header 来决定的，而且是需要两边都设置才有效。而一种轮询方式是否为长轮询，是根据服务端的处理方式来决定的，与客户端没有关系。
第二个区别就是实现的方式，连接的长短是通过协议来规定和实现的。而轮询的长短，是服务器通过编程的方式手动挂起请求来实现的。

### webhooks，websocket 和 SSE(Server-sent Events)

webhooks 请求发过去，，提供一个接口用来给服务端 push 数据过来，，实现通信
websocket 双工通道通信，，安全的 WebSocket 连接机制和 HTTPS 类似。首先，浏览器用 wss://xxx 创建 WebSocket 连接时，会先通过 HTTPS 创建安全的连接，然后，该 HTTPS 连接升级为 WebSocket 连接，底层通信走的仍然是安全的 SSL/TLS 协议。
所谓 SSE（Sever-Sent Event），就是浏览器向服务器发送一个 HTTP 请求，保持长连接，服务器不断单向地向浏览器推送“信息”（message），这么做是为了节约网络资源，不用一直发请求，建立新连接。sse 是单向的，只能由服务端往客户端发。

### 浏览器垃圾回收机制

### 长短轮询实现

```js
function Polling() {
  fetch(url)
    .then((data) => {
      // somthing
    })
    .catch((err) => {
      console.log(err);
    });
}
//每5s执行一次
setInterval(polling, 5000);
```

```js
function LongPolling() {
  fetch(url)
    .then((data) => {
      LongPolling();
    })
    .catch((err) => {
      LongPolling();
      console.log(err);
    });
}
LongPolling();
```

### this 机制

this 在任何情况下都不指向函数的词法作用域。

this 在运行时绑定，它的上下文取决于函数调
用时的各种条件。this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。

### 闭包

1. 在函数内部定义新函数
2. 新函数访问外层函数的局部变量，即访问外层函数环境的活动对象属性
3. 新函数执行，创建新函数的执行上下文，外层函数即为闭包

#### 闭包简单说明

闭包是一个 function,只能说 function xxx 对于外部 function yyy 来说形成了闭包
函数式编程的核心概念就是将函数存到变量里，用在参数里，用在返回值里
在编程时要时刻记住将无副作用与有副作用代码分开

对于闭包的形成，实质上涉及的就是词法作用域和将函数作为 return 值

如果一个函数内部有闭包存在，那么函数执行结束后不会释放自己的变量对象，只有当闭包执行结束后才会释放，因此闭包将会占用更大的内存空间，用于存储外层函数的变量兑现。

闭包：函数嵌套函数，内部函数可以引用外部函数的参数和变量，变量和参数不会被垃圾回收机制所回收
好处：

- 希望一个变量长期驻扎在内存之中
- 避免全局变量的污染
- 私有成员的存在
  坏处：
- 可能会造成内存泄漏

#### once 的例子

once 是一个高阶函数，返回值是一个函数，
如果 done 是 false，则将 done 设为 true，然后执行 fn。
done 是在返回函数的同一层，所以会被闭包记忆获取到

```js
const once = (fn) => {
  let done = false;
  return function () {
    // 三目多个操作时可以用小括号加逗号隔开
    return done ? undefined : ((done = true), fn.apply(this, arguments));
  };
};

let init_data = once(() => {
  console.log("Initialize data");
});

init_data(); // 输出Initialize data
init_data(); // done的值被缓存,不输出
```

#### 闭包的作用

1. 可以使用同级的作用域
2. 读取其他元素的内部变量
3. 延长作用域

函数闭包之后就可以完成一些正常情况访问不到的作用域，比如父级访问子集，同级之间的相互访问，让函数变得更加灵活。
闭包的变量不会被垃圾回收所以有一定的内存占用问题，怎么可以做到不浪费内存呢，可以用 result=null 清除没有被垃圾回收的数据。

#### 闭包内存

1. 资料链接
   [作用域链](http%3A//blog.csdn.net/u010425776/article/details/53557942)
   [对象内存模型](http%3A//blog.csdn.net/u010425776/article/details/53617292)
   [创建对象方式](http%3A//blog.csdn.net/u010425776/article/details/53704179)
2. 代码举例

```js
function father() {
  var name = "爸爸";
  var company = "Google";
  function son() {
    var name = "儿子";
    var school = "启东中学";
    alert(name);
    alert(school);
  }
  return son;
}
```

3. 分析:
   这段代码在 window 作用域中定义了函数 father；
   father 中有两个局部变量：name、company；
   father 中定义了一个闭包；
   闭包中定义了两个局部变量：name、school。

<!-- ![](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed/hexo/knowledge/nodeJsPic/cPack.png) -->

[![XpByC9.png](https://s1.ax1x.com/2022/05/23/XpByC9.png)](https://imgtu.com/i/XpByC9)

### JS 的 IIFE

IIFE: Immediately Invoked Function Expression，意为立即调用的函数表达式，也就是说，声明函数的同时立即调用这个函数。
JS 只有全局作用域（global scope）、函数作用域（function scope），从 ES6 开始才有块级作用域（block scope）
在访问控制这方面是多么的脆弱！那么如何实现作用域的隔离呢？在 JS 中，只有 function，只有 function，只有 function 才能实现作用域隔离，因此如果要将一段代码中的变量、函数等的定义隔离出来，只能将这段代码封装到一个函数中。

#### IIFE 形式的函数调用

```js
(function foo() {
  var a = 10;
  console.log(a);
})();
```

#### IIFE 的函数名和参数

```
var a = 2;
(function IIFE(global){
    var a = 3;
    console.log(a); // 3
    console.log(global.a); // 2
})(window);

console.log(a); // 2
```

#### IIFE 构造单例模式

普通实现:

```
function myModule(){
  var someThing = "123";
  var otherThing = [1,2,3];

  function doSomeThing(){
    console.log(someThing);
  }

  function doOtherThing(){
    console.log(otherThing);
  }

  return {
    doSomeThing:doSomeThing,
    doOtherThing:doOtherThing
  }
}

var foo = myModule();
foo.doSomeThing();
foo.doOtherThing();

var foo1 = myModule();
foo1.doSomeThing();
```

IIFE

```
var myModule = (function module(){
  var someThing = "123";
  var otherThing = [1,2,3];

  function doSomeThing(){
    console.log(someThing);
  }

  function doOtherThing(){
    console.log(otherThing);
  }

  return {
    doSomeThing:doSomeThing,
    doOtherThing:doOtherThing
  }
})();

myModule.doSomeThing();
myModule.doOtherThing();
```

#### 小结

IIFE 的目的是为了隔离作用域，防止污染全局命名空间。

### turborepo

所谓 Turborepo 是一个用于 JavaScript/TypeScript monorepos 的快速构建系统。

### 数据精度丢失问题

在 JavaScript 中，Number 类型范围-2^53 + 1 到 2^53 - 1
ES6 引入了 Number.MAX_SAFE_INTEGER 和 Number.MIN_SAFE_INTEGER 这两个常量，用来表示这个范围的上下限。
Number.isSafeInteger()用来判断一个整数是否落在这个范围之内。
在 Java 中 Long 类型的取值范围是-2^63 + 1 到 2^63 - 1, 比 JavaScript 中大很多，所以后端能正常处理。

```js
console.log(10976458979374927);
console.log(10976458979374928);
console.log(10976458979374929);
Number.isSafeInteger(10976458979374927);
Number.isSafeInteger(10976458979374928);
Number.isSafeInteger(10976458979374929);
```

## 拓展资料

(为什么模块循环依赖不会死循环？CommonJS 和 ES Module 的处理不同？)[https://mp.weixin.qq.com/s/5CYvCdq9s8e0j-0qlm3StA]
