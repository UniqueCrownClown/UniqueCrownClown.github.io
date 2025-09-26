---
title: interview Question
date: 2022-01-03 11:23:51
categories: interview
tags: [interview]
---

interview Question...

<!-- more -->

## 知识复习

### 无聊经典

- 盒子模型

IE 盒子模型 宽度=内容宽度+padding *2+border *2
w3c 盒子模型 宽度=内容宽度
通过 box-sizing 切换，默认为 content-box(w3c 盒子模型)，border-box 时为 IE 盒子模型

### 八股文

#### Https

对称------AES DES
非对称------RSA

公钥通常存放在客户端，私钥通常存放在服务器。使用公钥加密的数据只有用私钥才能解密，反过来使用私钥加密的数据也只有用公钥才能解密。

任何正版操作系统都会将所有主流 CA 机构的公钥内置到操作系统当中，所以我们不用额外获取，解密时只需遍历系统中所有内置的 CA 机构的公钥，只要有任何一个公钥能够正常解密出数据，就说明它是合法的。

先用非对称搞个双方知道的密钥，再用该密钥来对称加密传输。。

CA 的作用，，让浏览器能拿到网站的公钥。。。

#### 三次握手

1. 发送端首先发送一个带有 SYN（synchronize）标志地数据包给接收方。(SYN(seq=a))
2. 接收方接收后，回传一个带有 SYN/ACK 标志的数据包传递确认信息，表示我收到了。(SYNACK(seq=b,ack=a+1))
3. 最后，发送方再回传一个带有 ACK 标志的数据包，代表我知道了，表示握手结束。(ACK(seq=a+1,ack=b+1))

#### 四次挥手

1. Client 发送一个 FIN，用来关闭 Client 到 Server 的数据传送，Client 进入 FIN_WAIT_1 状态(FIN(seq=a))
2. Server 收到 FIN 后，发送一个 ACK 给 Client，确认序号为收到序号+1（与 SYN 相同，一个 FIN 占用一个序号），Server 进入 CLOSE_WAIT 状态。(ACK(seq=a+1))
3. Server 发送一个 FIN，用来关闭 Server 到 Client 的数据传送，Server 进入 LAST_ACK 状态。(FIN(seq=b，ack=a+1))
4. Client 收到 FIN 后，Client 进入 TIME_WAIT 状态，接着发送一个 ACK 给 Server，确认序号为收到序号+1，Server 进入 CLOSED 状态，完成四次挥手(ACK(seq=b+1))

- HTTP1.0 和 HTTP1.1

HTTP1.0 的每一次请求都伴随着一次三次握手的过程，并且是串行的请求，增加了不必要的性能开销
HTTP1.1 新增了长链接的通讯方式，减少了性能损耗

HTTP1.0 只有串行发送，没有管道
HTTP1.1 增加了管道的概念，使得在同一个 TCP 链接当中可以同时发出多个请求

HTTP1.0 不支持断点续传
HTTP1.1 新增了 range 字段，用来指定数据字节位置，开启了断点续传的时代

HTTP1.0 任务主机只有一个节点，所以并没有传 HOST
HTTP1.1 时代，虚拟机技术越来越发达，一台机器上也有可能有很多节点，故增加了 HOST 信息

在 HTTP1.0 中主要使用 header 里的 If-Modified-Since,Expires 来做为缓存判断的标准

HTTP1.1 则引入了更多的缓存控制策略例如 Entity tag，If-Unmodified-Since, If-Match, If-None-Match 等更多可供选择的缓存头来控制缓存策略。

在 HTTP1.1 中新增了 24 个错误状态响应码，如 410（Gone）表示服务器上的某个资源被永久性的删除等。

- HTTP1.1 和 HTTP2

HTTP 协议的报文是由「Header + Body」构成的，对于 Body 部分，HTTP/1.1 协议可以使用头字段 「Content-Encoding」指定 Body 的压缩方式，比如用 gzip 压缩，这样可以节约带宽，但报文中的另外一部分 Header，是没有针对它的优化手段。

HTTP/2 没使用常见的 gzip 压缩方式来压缩头部，而是开发了 HPACK 算法，客户端和服务器两端都会建立和维护「字典」，用长度较小的索引号表示重复的字符串，再用 Huffman 编码压缩数据，可达到 50%~90% 的高压缩率。

HTTP1.1 采用明文的形式
HTTP/2 全⾯采⽤了⼆进制格式，头信息和数据体都是⼆进制

HTTP/2 的数据包不是按顺序发送的，同⼀个连接⾥⾯连续的数据包，可能属于不同的回应。(对数据包做了标记，标志其属于哪一个请求，其中规定客户端发出的数据流编号为奇数，服务器发出的数据流编号为偶数。客户端还可以指定数据流的优先级，优先级⾼的请求，服务器就先响应该请求)

HTTP/2 可以在⼀个连接中并发多个请求或回应。
如:在⼀个连接中，服务器收到了客户端 A 和 B 的两个请求，但是发现在处理 A 的过程中⾮常耗时，索性就先回应 A 已经处理好的部分，再接着回应 B 请求，最后再回应 A 请求剩下的部分。

服务器可以主动向客户端发送请

- HTTP2 和 HTTP3

HTTP2 是基于 TCP 协议实现的
HTTP3 是基于 UDP 协议实现的

UDP 的无连接，TCP 的连接，唯区别是，UDP 把只管发送，TCP 每次都对发送的数据进行 ACK 确认。

HTTP3 新增了 QUIC 协议来实现可靠性的传输
QUIC（Quick UDP Internet Connection）是谷歌制定的一种基于 UDP 的低时延的互联网传输层协议。

HTTP2 是基于 HTTPS 实现的，建立连接需要先进行 TCP 3 次握手，然后再进行 TLS 3 次握手，总共 6 次握手
HTTP3 只需要 QUIC 的 3 次握手

- HTTP 3.0 基于 udp 的话, 如何保证可靠的传输?

UDP 想要实现可靠性传输，通常的做法是在应用层模拟 TCP 的可靠性传输
HTTP 3.0 用的是 QUIC
并不意味着 QUIC 本身也是不可靠的！在许多方面，QUIC 应该被看作是一个 TCP 2.0。它包括 TCP 的所有特性（可靠性、拥塞控制、流量控制、排序等）的最佳版本，以及更多其他特性。QUIC 还完全集成了 TLS（参见图 6），并且不允许未加密的连接。

2022 年 6 月 6 日，IETF QUIC 和 HTTP 工作组成员 Robin Mark 在推特上宣布，历时 5 年，HTTP/3 终于被标准化为 RFC 9114 ，这是 HTTP 超文本传输协议的第三个主要版本。

- babel 与 polyfill 的关系以及区别

Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码。注意：Babel 默认只转换新的 JavaScript 句法（syntax），而不转换新的 API。

Polyfill 的准确意思为，用于实现浏览器并不支持的原生 API 的代码。

js--babel parse-->ast--plugins transforms-->ast-->babel generate -->js

babel-polyfill.js

babel-polyfill 是好东西，能够将新 API 作用在老的浏览器上。

@babel/runtime & @babel/plugin-transform-runtime 按需引入, 打包体积小 不能兼容实例方法

@babel/polyfill 完整模拟 ES2015+ 环境 打包体积过大、污染全局对象和内置的对象原型

@babel/preset-env 按需引入, 可配置性高 不支持 stage-x 插件，没有利用针对特定浏览器的功能

#### Https 证书 ca 的作用

CA 就有作用了，为了保证客户端获得的公钥是可靠的，不是别人冒充，CA 用于给网站颁发一个可信的证书
CA 就用于保证，你获得的公钥确实是你要访问的可靠网站的公钥，而不是别人冒充的。
证书在网站申请的时候便由 CA 生成。因为生成证书会用到 CA 私钥。
网站申请时，需要提供自己的公钥。 因为正式描述信息里包含了网站的公钥。

冒充因为没有正确私钥，就算得到正牌网站的公钥，也无法解出密文

#### 从在浏览器地址栏中输入 URL 到页面显示，浏览器到底发生了什么

chrome 的主要进程架构
浏览器进程 (Browser Process)：负责浏览器的 TAB 的前进、后退、地址栏、书签栏的工作和处理浏览器的一些不可见的底层操作，比如网络请求和文件访问。
渲染进程 (Renderer Process)：负责一个 Tab 内的显示相关的工作，也称渲染引擎。
插件进程 (Plugin Process)：负责控制网页使用到的插件
GPU 进程 (GPU Process)：负责处理整个应用程序的 GPU 任务

首先，当我们是要浏览一个网页，我们会在浏览器的地址栏里输入 URL，这个时候 Browser Process 会向这个 URL 发送请求，获取这个 URL 的 HTML 内容，然后将 HTML 交给 Renderer Process，Renderer Process 解析 HTML 内容，解析遇到需要请求网络的资源又返回来交给 Browser Process 进行加载，同时通知 Browser Process，需要 Plugin Process 加载插件资源，执行插件代码。解析完成后，Renderer Process 计算得到图像帧，并将这些图像帧交给 GPU Process，GPU Process 将其转化为图像显示屏幕。

构建 DOM 过程中，如果遇到<script>标签，渲染引擎会停止对 HTML 的解析，而去加载执行 JS 代码，原因在于 JS 代码可能会改变 DOM 的结构（比如执行 document.write()等 API）
不过开发者其实也有多种方式来告知浏览器应对如何应对某个资源，比如说如果在<script> 标签上添加了 async 或 defer 等属性，浏览器会异步的加载和执行 JS 代码，而不会阻塞渲染。

浏览器的多进程架构，根据不同的功能划分了不同的进程，进程内不同的使命划分了不同的线程，当用户开始浏览网页时候，浏览器进程进行处理输入、开始导航请求数据、请求响应数据，查找新建渲染进程，提交导航，之后渲染又进行了解析 HTML 构建 DOM、构建过程加载子资源、下载并执行 JS 代码、样式计算、布局、绘制、合成，一步一步的构建出一个可交互的 WEB 页面，之后浏览器进程又接受页面的交互事件信息，并将其交给渲染进程，渲染进程内主进程进行命中测试，查找目标元素并执行绑定的事件，完成页面的交互。

1. 缓存原理

2. 渲染原理

### 项目问题

1. 项目是如何部署的

jenkins java 开发的持续集成工具
是个部署在 tomcat 上的 war 包

检查是否开启服务器的 ssh 服务，ubuntu 默认不开启,netstat -anp | grep :22
localhost:8080/jenkins
jenkins 系统管理,新建节点,全局工具配置中配置 git 命令
编写 shell 脚本
拉取同步代码,maven 打包,停止 tomcat,部署应用,启动 tomcat,验证结果
创建自动化部署任务

- 创建 Jenkins 任务
- 填写 Server 信息
- 配置 git 参数
- 填写构建语句(shell 脚本)

后续代码如果有改动，只要 push 到 github 或者 gitlab 等上，在 jenkins 界面中再次执行构建任务就可以了，非常方便，自动化部署，再也不用手动上传项目到服务器了。

2. promise 的 then 的第二个参数和 catch 的执行时序?

**如果是 promise 内部报错，reject 抛出错误后，then 的第二个参数和 catch 方法都存在的情况下，只有 then 的第二个参数能捕获到，如果 then 的第二个参数不存在，则 catch 方法会捕获到。**

**如果在 then 的第一个函数里抛出了异常，后面的 catch 能捕获到，而 then 的第二个函数捕获不到。**

第二种写法要好于第一种写法，理由是第二种写法可以捕获前面 then 方法执行中的错误，也更接近同步的写法（try/catch）。因此，**建议总是使用 catch 方法，而不使用 then 方法的第二个参数。**

3. webpack 有没有分包经验

- splitChunksPlugin

- 动态 import

- 模块联邦

- 确保启用Tree-shaking，移除未使用的代码，减少打包后的文件大小

- webpack-bundle-analyzer

- 缓存策略
  利用浏览器缓存来减少重复请求，提高加载速度。可以通过设置 filename 和 chunkFilename 来控制缓存文件名。
  对于频繁更新的模块，可以使用 [contenthash] 或 [chunkhash] 来确保缓存的有效性。

4. npm run xxx 的执行逻辑
   运行 npm install @vue/cli-service 的时候，npm 在 node_modules/.bin/目录中创建 vue-cli-service 为名的可执行文件

npm run serve --> package.json 的 scripts 下的 serve-->vue-cli-service serve-->node_modules/.bin 看看同名的可执行文件，有，使用其运行-->node_modules/.bin/vue-cli-service.cmd serve-->使用 node 运行 vue-cli-service.js

5. 规范

规范是每家公司都有的，简单说下

lint-staged，执行一些操作，如：prettier、eslint。
husky，git 钩子。

**ESlint 会在格式化代码的时候，去修复代码中的错误，而 Prettier 更多地是去格式化代码而忽略代码中的错误。**

pre-commit，在 git commit 前执行 lint-staged。
commit-msg，在 git commit 后执行 commitlint。

commitizen，执行 git cz 的时候可规范进行提交 commit。
standard-version，更新 changeLog.md 以及 package.json 的 version

6. 开发流程

- 开发完成后，提交代码到远程仓库，远程仓库有三个基本分支（其他的都是功能分支），分别是 beta、preview、master。
- 一般本地开发完成会把自己的功能分支合并到 beta 分支提交上去测试。
- 测试没问题了就合并到主分支，然后执行 release 命令生成修改版本号以及生成 changeLog。
- 最后 push 到远程仓库。

7. CommonJS 和 ES Module 的差异

一个是导出值的拷贝，一个是导出值的引用；一个是运行时加载，一个是静态编译

对于循环引用的解决原理

CommonJS 的 module.exports 和 exports 有什么不同

- 当绑定一个属性时，两者相同
- 不能直接赋值给 exports，也就是不能直接使用 exports={}这种语法

CommonJS 的引入特点是值的拷贝，简单来说就是把导出值复制一份，放到一块新的内存中。

AB 模块间的互相引用，本应是个死循环，但是实际并没有，因为 CommonJS 做了特殊处理——模块缓存

每一个模块都先加入缓存再执行，每次遇到 require 都先检查缓存，这样就不会出现死循环；借助缓存，输出的值也很简单就能找到了。

CommonJS 是把暴露的对象拷贝一份，放在新的一块内存中，每次直接在新的内存中取值，所以对变量修改没有办法同步；而 ES Module 则是指向同一块内存，模块实际导出的是这块内存的地址，每当用到时根据地址找到对应的内存空间，这样就实现了所谓的“动态绑定”。

ES Module 来处理循环使用一张模块间的依赖地图来解决死循环问题，标记进入过的模块为“获取中”，所以循环引用时不会再次进入；使用模块记录，标注要去哪块内存中取值，将导入导出做连接，解决了要输出什么值。

路径解析规则

- 对于核心模块，node 将其已经编译成二进制代码，直接书写标识符 fs、http 就可以
- 对于自己写的文件模块，需要用‘./’'../'开头，require 会将这种相对路径转化为真实路径，找到模块
- 对于第三方模块，也就是使用 npm 下载的包，就会用到 paths 这个变量，会依次查找当前路径下的 node_modules 文件夹，如果没有，则在父级目录查找 no_modules，一直到根目录下，找到为止。

- 比如面试官问：你给我讲一下闭包吧？

```
1、解释是什么的问题。
2、解释这个技术的应用点、应用场景在哪里。
3、整理一下这个问题的优缺点是什么。
```

```
1. 闭包是：能够访问其他函数内部变量的函数。
2. 闭包一般会在：封装模块的时候，通过函数自执行函数的方式进行实现；或者在模仿块级作用域的时候实现；如：我们常用的库jQuery本身就是一个大的闭包。
3. 闭包的优点是：
    a、能够在离开函数之后继续访问该函数的变量，变量一直保存在内存中。
    b、闭包中的变量是私有的，只有闭包函数才有权限访问它。不会被外面的变量和方法给污染。
闭包的缺点是：
    a、会增加对内存的使用量，影响性能。
    b、不正确的使用闭包会造成内存泄漏。
```

8. 新版本发布后,怎么用技术手段通知用户刷新页面?

- 使用 Webpack 构建命令。在每次发布前端时，使用 Webpack 构建命令生成一个包含版本号的 json 文件。在项目中，通过定时任务或在切换页面路由时，请求这个 json 文件。比较 json 文件中存储的版本号和本地保存的上一次版本号，如果不一致，则强制刷新页面。
- 前后端配合。在每个请求的 header 里面加上发版版本号，和保留在客户端的上一次版本号进行比较。如果不一致，则强制刷新页面。
- 使用 JavaScript 或 CSS 实现自定义提示框。在页面更新后，可以使用 JavaScript 或 CSS 创建一个提示框，提示用户页面需要刷新。用户可以点击按钮来强制刷新页面。
  使用浏览器自带的更新提示。浏览器会在页面更新时自动弹出更新提示框，提示用户是否刷新页面。
- 使用 Service Worker 和 Web Push API。这些技术可以向用户发送推送通知，提示用户刷新页面以加载新版本。

9. 性能优化数据怎么上报、分析？

10. vue3 在某些场景比 vue2 性能更低，为什么会这样？

11. 功能引导、错误上报等 sdk 封装

12. webpack的module、bundle、chunk分别指的是什么？

### 新鲜架构

#### webpack 5 内置的 ModuleFederationPlugin(模块联邦)

(京东零售平台：前端组件资源共享与中心化管理实践)[https://mp.weixin.qq.com/s?__biz=MzUxMzcxMzE5Ng==&mid=2247517881&idx=1&sn=90a9ae4663188e4da1a5961e8ac3b013&chksm=f95233face25baec3975677e3b7d71f19ed48bc2c060f8c02da30477f8dbf03b51eead8ba051&scene=21#wechat_redirect]

优点: 相比于 npm 包,更新链路短,修改后第三方平台无需更新依赖重新构建
组成: mf-cli + 组件共享平台 + nginx 转发

#### 常见 http 状态码

- 200
- 301 重定向
- 401 (Unauthorized/未授权)
