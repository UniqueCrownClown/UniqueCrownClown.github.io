---
title: js埋点监控
date: 2022-06-21 11:23:51
categories: js #文章分类
tags: [js埋点监控] #文章标签，可以一次添加多个标签
---

埋点监控...

<!-- more -->

### sentry

使用 web bug 埋点追踪系统，目前开源免费好用的系统，就是今天要介绍的 sentry
sentry 是个很优秀的 bug 追踪系统，为上线应用获取 bug 信息，隐藏 source map 有着极其重要的贡献

可以通过上传sourceMap的方式定位到具体的错误

可以用 docker 和 compose 实现私有化部署

### 概念

实现前端监控，第一步肯定是将我们要监控的事项（数据）给收集起来，再提交给后台进行入库，最后再给数据分析组进行数据分析，最后处理好的数据再同步给运营或者是产品。

### 职能范围

#### 用户行为监控

负责统计 PV（页面访问次数）、UV（页面访问人数）以及用户的点击操作等行为

#### 页面性能监控

页面加载时间、白屏时间等

The time to first-paint was 白屏时间
The time to first-contentful-paint was 首屏时间

白屏时间 = 页面开始展示的时间点 - 开始请求时间点
开始请求时间点: Performance Timing.navigation.Start
页面开始展示的时间点: head标签末尾插入script统计时间节点或者直接用Performance Timing.domLoading

首屏时间 = 首屏内容渲染结束时间点 - 开始请求时间点

首屏渲染时间: PerformanceTiming有一个domInteractive属性，代表了DOM结构结束解析的时间点，就是Document.ready State属性变为“interactive”
#### 错误报警监控

除了全局捕获到的错误信息，还有在代码内部被 catch 住的错误告警，这些都需要被收集到。

- try catch

- windows.onerror

### 埋点上报

- 手动埋点
  即纯手动写代码，调用埋点 SDK 的函数，在需要埋点的业务逻辑功能位置调用接口，上报埋点数据
- 可视化埋点
  通过可视化交互的手段，代替上述的代码埋点。将业务代码和埋点代码分离，提供一个可视化交互的页面，输入为业务代码，通过这个可视化系统，可以在业务代码中自定义的增加埋点事件等等，最后输出的代码耦合了业务代码和埋点代码
- 无埋点
  前端自动采集全部事件，上报埋点数据，由后端来过滤和计算出有用的数据。

### sdk 设计

#### 数据发送

现在的埋点监控工具通常会优先使用 sendBeacon，但由于浏览器兼容性，还是需要用图片的 src 兜底

#### 用户行为监控

#### 页面性能

页面的性能数据可以通过 performance.timing 这个 API 获取到，获取的数据是单位为毫秒的时间戳。

比较关键的数据有下面几个，根据它们可以计算出 FP/DCL/Load 等关键事件的时间点：

页面首次渲染时间：FP(firstPaint)=domLoading-navigationStart
DOM 加载完成：DCL(DOMContentEventLoad=domContentLoadedEventEnd-navigationStart
图片、样式等外链资源加载完成：L(Load)=loadEventEnd-navigationStart

#### 用户告警

1. js 原生错误
   除了 try catch 中捕获住的错误，我们还需要上报没有被捕获住的错误——通过 error 事件和 unhandledrejection 事件去监听。
2. error 事件和 unhandledrejection 事件

- InternalError: 内部错误，比如如递归爆栈;
- RangeError: 范围错误，比如 new Array(-1);
- EvalError: 使用 eval()时错误;
- ReferenceError: 引用错误，比如使用未定义变量;
- SyntaxError: 语法错误，比如 var a = ;
- TypeError: 类型错误，比如[1,2].split('.');
- URIError: 给  encodeURI 或  decodeURl()传递的参数无效，比如 decodeURI('%2')
- Error: 上面 7 种错误的基类，通常是开发者抛出

Promise 内部抛出的错误是无法被 error 捕获到的，这时需要用 unhandledrejection 事件。

3. react 错误处理机制
   生命周期是 getDerivedStateFromError 和 componentDidCatch
4. vue 错误处理机制
   有一个类似的生命周期来做这件事，不再赘述：errorCaptured

(参考仓库)[https://github.com/M-cheng-web/web-tracing]
