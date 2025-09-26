---
title: nodeJs,npm
date: 2017-06-21 11:23:51
categories: js #文章分类
tags: [nodeJs, npm] #文章标签，可以一次添加多个标签
---

nodejs...

<!-- more -->

### 简介

基于 V8 引擎的 javascript 运行时

### nodejs 版本管控

使用 nvm 来做版本控制
nvm-windows 工具
nvm ls // 查看目前已经安装的版本
nvm install 12.5.0 // 安装指定的版本的 nodejs
nvm use 12.5.0 // 使用指定版本的 nodejs
https://github.com/coreybutler/nvm-windows/releases

### Node.js stream 类型

流是有方向的数据,按照流动方向可以分为三种流

1. 设备流向程序：readable
2. 程序流向设备：writable
3. 双向流动：duplex、transform

用流后代码逻辑变得相对独立，可维护性也会有一定的改善

```js
// 用管道读流流动到写流
const fs = require("fs");
const FILEPATH = "DEST_PATH_IN_YOUR_DISK";

const rs = fs.createReadStream(FILEPATH);
const ws = fs.createWriteStream(DEST);

rs.pipe(ws);
```

称 pipe() 连接的流为加工器,可以用双向流的操作对数据进行加工

```js
// 对文件进行转小写操作
const fs = require("fs");
const rs = fs.createReadStream("./package.json");
const ws = fs.createWriteStream("./package-lower.json");
rs.pipe(lowercase).pipe(ws);

// 比如字母还需要转成 ASCII 码，假定有一个双工流 ascii，那么代码简单改写
rs.pipe(lowercase).pipe(acsii).pipe(ws);
```

point: HTTP 协议的 Transfer-Encoding: chunked (分段传输特性)
用户 Web 在线看视频的场景，通过 HTTP 请求返回给用户电影内容
写法 1 读完文件返回响应

```js
const http = require("http");
const fs = require("fs");

http
  .createServer((req, res) => {
    fs.readFile(moviePath, (err, data) => {
      res.end(data);
    });
  })
  .listen(8080);
```

写法 2 用管道流分段传输

```js
const http = require("http");
const fs = require("fs");
const oppressor = require(oppressor);
http
  .createServer((req, res) => {
    fs.createReadStream(moviePath).pipe(res);
    // 可引入对视频功能进行压缩的双工流
    fs.createReadStream(moviePath).pipe(oppressor).pipe(res);
  })
  .listen(8080);
```

### 位运算符操作

位运算符在 js 中的妙用：

1. 使用&运算符判断一个数的奇偶

偶数 & 1 = 0
奇数 & 1 = 1
那么 0&1=0,1&1=1

2. 使用~~，>>,<<,>>>,|来取整

~~3.14 = 3
3.14 >> 0 = 3
3.14 << 0 = 3 3.14 | 0 = 3 3.14 >>> 0 = 3(>>>不可对负数取整)
注意：~~-3.14 = -3 其它的一样

3. 使用<<,>>来计算乘除

乘法：
1\*2 = 2
1<>1 = 1(2/2 的一次方)

4. 利用^来完成比较两个数是否相等

1 ^ 1 = 0
1 ^ 非 1 数 ！=0
所以同一个数……同一个数等于 0，否则不等于 0

5. 使用^来完成值交换

```js
a = 1;
b = 2;
a ^= b;
b ^= a;
a ^= b;
// 结果a=2，b=1
```

6. 使用&,>>,|来完成 rgb 值和 16 进制颜色值之间的转换

- 16 进制颜色值转 RGB：

```js
function hexToRGB(hex) {
  var hex = hex.replace("#", "0x"),
    r = hex >> 16,
    g = (hex >> 8) & 0xff,
    b = hex & 0xff;
  return "rgb(" + r + "," + g + "," + b + ")";
}
```

- RGB 转 16 进制颜色值：

```js
function RGBToHex(rgb) {
  var rgbArr = rgb.split(/[^\d]+/),
    color = (rgbArr[1] << 16) | (rgbArr[2] << 8) | rgbArr[3];
  return "#" + color.toString(16);
}
```

### 私有 npm 源实现

    verdaccio(基于 Node.js 开发)

### 新流行前端框架

    svelte
    solid
    不虚拟dom直接编译成dom操作的流行框架

### deno 入门概念了解

[https://deno.land/](https://deno.land/)

```shell
npm i -g pnpm
```

项目根目录下的依赖路径通过软链接链接到.pnpm 下的@版本/node_module 下,,该 node_modules 的依赖又通过硬链接链到全局的 pnpmStore 目录下

#### peerDependencies

peerDependencies 的具体作用：
peerDependencies 的目的是提示宿主环境去安装满足插件 peerDependencies 所指定依赖的包，然后在插件 import 或者 require 所依赖的包的时候，永远都是引用宿主环境统一安装的 npm 包，最终解决插件与所依赖包不一致的问题。

这对于写 npm 包非常重要。
当你写的包 a 里面依赖另一个包 b，而这个包 b 是引用这个包 a 的业务的常用的包的时候，建议写在 peerDependencies 里，避免重复下载/多个版本共存

#### peerDependenciesMeta

#### nodejs 的 execa 库使用

Nodejs 常见调用子进程的方法

1. child_process 自带模块
2. ShellJS
3. cli
4. execa(推荐)
5. node-ssh

```bash
mkdir demo && cd demo
npm init -y # 项目初始化 或者 yarn init -y

npm i execa --save # yarn add execa --save
npm install babel-core --save-dev
npm install babel-register --save-dev
```

```js
const execa = require("execa");
execa("ls").then((result) => {
  console.log(result);
  console.log(result.stdout);
});
```

#### nodejs 酷包

1. chokidar 可以用于监控文件、文件夹变化，我们可以传入 glob 文件匹配模式，并可以简单实现递归目录监控。chokidar 可以监控各种文件、文件夹变化事件，包含 add , change , unlink , addDir , unlinkDir 等。
2. execa 调用子进程
3. fs-extra 模块是系统 fs 模块的扩展,提供了更多便利的 API

#### npm run xxx

npm run serve -->vue-cli-service serve-->node vue-cli-service
软链接

npm i @vue/cli-service，npm 在 安装这个依赖的时候，
node_modules/.bin/ 目录中创建 好 vue-cli-service 为名的几个可执行文件了。

所以当我们运行 vue-cli-service serve 这条命令的时候，就相当于运行 node_modules/.bin/vue-cli-service.cmd serve。
然后这个脚本会使用 node 去运行 vue-cli-service.js 这个 js 文件
由于 node 中可以使用一系列系统相关的 api ，所以在这个 js 中可以做很多事情，例如读取并分析运行这条命令的目录下的文件，根据模板生成文件等

1. 运行 npm run xxx 的时候，npm 会先在当前目录的 node_modules/.bin 查找要执行的程序，如果找到则运行；
2. 没有找到则从全局的 node_modules/.bin 中查找，npm i -g xxx 就是安装到到全局目录；
3. 如果全局目录还是没找到，那么就从 path 环境变量中查找有没有其他同名的可执行程序。

### nodeJS collect

1. path.resolve 总是返回一个以相对于当前的工作目录（working directory）的绝对路径。

```js
const path = require("path");
path.resolve("/foo/bar", "./baz"); // return /foo/bar/baz
```

### nodeJS 事件循环

和浏览器的事件循环(event loop),nodejs V11.0 以上 这两者之间的顺序就相同了

### nodeJS 线程模型

要理解 node 的线程模型，首先要了解 node 的架构，node 的底层是 libuv，v8 两大支架及一些较小的依赖如 node12 中引进来更快的 llhttp，c-ares，icu-data，zlib 等。因此要学好 node，则要了解 libuv 以及 v8 两大块，其一 libuv 与事件循环息息相关，无时无刻体现在你的代码中，而 v8 与 cpu/memory/gc 相关，无时无刻体现在你的线上问题排查中。

### pm2

pm2 是可以用于生产环境的 Nodejs 的进程管理工具，并且它内置一个负载均衡。它不仅可以保证服务不会中断一直在线，并且提供 0 秒 reload 功能，还有其他一系列进程管理、监控功能。

(官方文档)[https://pm2.keymetrics.io/docs/usage/quick-start/]

### simple collection

1. process.cwd()和\_\_dirname

process.cwd() 是**当前 Node.js 进程执行时的文件夹地址**——工作目录，保证了文件在不同的目录下执行时，路径始终不变
\_\_dirname 是**被执行的 js 文件的地址** ——文件所在目录

2. 在 node 版本 v12.0.0 之前，是不支持直接用 node 执行.mjs 文件的，需要加上·--experimental-modules 选项，因为这时还是一个实验性的功能，所以还会爆出提示信息；在 node 版本 v12.0.0 之后，可以直接用 node 执行.mjs 文件。
