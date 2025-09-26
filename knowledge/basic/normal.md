---
title: 常见知识
date: 2017-09-16 11:23:51
categories: 知识笔记 #文章分类
tags: [常见知识] #文章标签，可以一次添加多个标签
---

常见知识...

<!-- more -->

## 概念

1. license

2. Web Worker

Web Worker 为 javascript 带来了什么，以及典型的应用场景。有强大的计算能力,可以加载一个 JS 进行大量的复杂计算而不挂起主进程，并通过 postMessage，onmessage 进行通信，解决了大量计算对 UI 渲染的阻塞问题。

- 数学运算
  Web Worker 最简单的应用就是用来做后台计算，对 CPU 密集型的场景再适合不过了。
- 图像处理
  通过使用从 canvas 中获取的数据，可以把图像分割成几个不同的区域并且把它们推送给并行的不同 Workers 来做计算，对图像进行像素级的处理，再把处理完成的图像数据返回给主页面。
- 大数据的处理
  目前 mvvm 框架越来越普及，基于数据驱动的开发模式也越愈发流行，未来大数据的处理也可能转向到前台，这时，将大数据的处理交给在 Web Worker 也是上上之策了吧。

3. chrome-devtools:

- Performance 工具可以用来录制一段时间的 CPU 占用、内存占用、FPS 等运行时性能问题
- Network 工具可以用来分析加载性能问题,优化 CPU 占用和 FPS 过低(fps>60 显示流畅，不能过低)

4.  iBeacon

5.  electron + nedb

6.  抛弃 koa-logger，拥抱 log4js

7.  取模

求两个数相除的余数

对质数取模的话，能尽可能地避免模数相同的数之间具备公因数，来达到减少冲突的目的。那么有个很大的且好记的质数 1e9+7(包括它的孪生素数 1e9+9)。

- 所有模过之后的数在加法操作在 int 范围内不会溢出，即 a,b<230,a+b<231a,b<2^{30},a+b<2^{31}a,b<2^{30},a+b<2^{31} 。

- 在乘法操作后在 long long 范围内不会溢出，即 ab<260ab<2^{60}ab<2^{60} 。

8. asm.js + Emscripten && WebAssembly && WebAssembly Explorer && WebAssembly Studio

9. pc vue 移动端 weex
   pc react 移动端 react-native
   移动端 微信小程序
   多端框架 omi uniapp taro

10. Flyio(类似 axios 的请求库)，在多个平台(微信小程序，node 端,浏览器环境)一致，支持复用

11. LLM large language model 大语言模型

12. localhost和127.0.0.1的区别
127.0.0.1是本地回环地址，它表示的是本地主机，是网络中的特殊IP地址。
localhost是域名，它表示的是本地主机，是网络中的特殊域名。 
localhost通过host文件会自动解析到127.0.0.1

13. CAP 和 BASE 理论

## BFF

BFF(Backend For Frontend)层基本上是一个编排器层(orchestrator layer)，也称为前端的后端。当编排器层包括聚合、计算、某些数据的组合时，它就不仅仅是一个简单的 API 网关。由于大多数公司出于一些原因（可扩展性、开发成本、可维护性等）而转向微服务架构。因此，考虑到每个客户端都有不同的规范和不同的数据要求，每个微服务都很难为其每个客户端提供准确的 API。
api 架构主流通信方式 GraphQL 和 REST

## 像素

视网膜屏幕
设备像素比（device pixel ratio） dpr (物理像素/css 像素)
移动端设备
CSS 像素又被称为逻辑像素、是以 px 为单位
物理像素又被称为设备像素、是以 pt 为单位
每个设备都有属于自己的独立的设备像素

css 像素不能单独拿出来说， 它与设备像素有一个对应关系，而浏览器帮你维护了这个对应关系。
[移动端适配](https://juejin.cn/post/6999438892441026591)

## 系统安装

ecfi 系统分区
ecfi 和 bois 启动
osm 分区
ESP/MSR 分区
easyuefi 的使用

1. mbr 和 gpt
   mbr 存在于磁盘驱动器开始部分的一个特殊的启动扇区
   当一台电脑启动时，它先启动主板自带的 bios 系统，
   bios 加载 mbr，mbr 再启动 windows
   MBR 分区表最多为什么只能有 4 个主分区
   gpt 是一种使用 uefi 启动的磁盘组织方式
   gpt 必须在 bios 下设置启动项
   硬盘支持格式(MBR 格式，GPT 格式)
   我的电脑现在都是 GPT 格式
2. uefi 和 legacy 的区别
   uefi 是新式的 BIOS，legacy 是传统 BIOS。
   UEFI：新模式，
   其启动顺序：开机 →UEFI 初始化 → 引导操作系统 → 进入操作系统启动。
   速度相对 lagacy 模式要更快
   Legacy：
   传统 BIOS 传输模式启动顺序：开机 →BIOS 初始化 →BIOS 自检 → 引导操作系统 → 进入系统。
   传统硬盘引导记录为 MBR 格式，MBR 无法支持超过 2T 的硬盘。但拥有最好的兼容性。

## 自动化测试工具 puppeteer

## CDP-Chrome Devtools Protocol

Chrome DevTools Protocol 允许使用工具来检测、检查、调试和分析 Chromium、Chrome 和其他基于 Blink 的浏览器。 许多现有项目目前使用该协议。并且 Chrome DevTools 使用此协议。因为功能比较多，所以分了多个域（一般复杂的东西都会分域），包括 DOM、Debugger、Network、Page 等等，分别放不同的调试协议。

## 如何提升FPS

每秒钟能够渲染的帧数

提升 FPS 可以通过减少 DOM 操作、使用 CSS3 动画、使用 requestAnimationFrame、图片优化、懒加载、使用Web Worker等方式来实现。这些技术可以帮助我们优化页面性能，提高用户体验。
## 日常小记

1. husky + pretty-quick + prettier 用来做代码 commit 时的自动格式化
2. ssh-keygen git bash 用来生成公私钥的命令
3. 破解页面禁止 f12(1.Alt+D 2.Ctrl+Shift+i)
4. 大驼峰,小驼峰,帕斯卡命名法(大驼峰,pascal),下划线命名法(蛇形命名法,UnderScoreCase),串式命名法(中杠)
5. window字体间距变大,冲突键: shift+shift+空格键
