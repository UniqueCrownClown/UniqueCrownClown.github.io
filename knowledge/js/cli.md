---
title: web构建cli
date: 2018-09-16 11:23:51
categories: js #文章分类
tags: [web构建cli] #文章标签，可以一次添加多个标签
---

web 构建 cli...

<!-- more -->

### 模块化

.mjs 是什么文件呢？很明显因为 ESM 和 CJS 的加载方式不同，为了更高的区分这两种不同的加载方式，于是创建了 .mjs(Module JavaScript).mjs 就是表示当前文件用 ESM 的方式进行加载，如果是普通的 .js 文件，则采用 CJS 的方式加载。

#### 发展历程

- IIFE： 使用自执行函数来编写模块化，特点：在一个单独的函数作用域中执行代码，避免变量冲突。
- AMD： 使用 requireJS 来编写模块化，特点：依赖必须提前声明好。
- CMD： 使用 seaJS 来编写模块化，特点：支持动态引入依赖文件。
- CommonJS： nodejs 中自带的模块化。
- UMD：兼容 AMD，CommonJS 模块化语法。
- webpack(require.ensure)：webpack 2.x 版本中的代码分割。
- ES Modules： ES6 引入的模块化，支持 import 来引入另一个 js 。

SystemJS 是一个通用的模块加载器，它能在浏览器或者 NodeJS 上动态加载模块，并且支持 CommonJS、AMD、全局模块对象和 ES6 模块。通过使用插件，它不仅可以加载 JavaScript，还可以加载 CoffeeScript 和 TypeScript。

#### js 各种模块写法

- UMD
  其是 amd 和 commonjs 的统一规范，支持两种规范，即写一套代码，可用于多种场景。其用法如下：
  前后端通用
  与 CJS 或 AMD 不同，UMD 更像是一种配置多个模块系统的模式。
  UMD 在使用诸如 Rollup/ Webpack 之类的 bundler 时通常用作备用模块

UMD 希望提供一个前后端跨平台的解决方案（支持 AMD 与 CommonJS 模块方式）
​ 代码结构:

- 先判断是否支持 Node.js 模块格式（exports 是否存在），存在则使用 Node.js 模块格式
- 再判断是否支持 AMD（define 是否存在），存在则使用 AMD 方式加载模块
- 前两个都不存在，则将模块公开到全局（window 或 global）

```js
(function (root, factory) {
  if (typeof define === "function" && define.amd) {
    // AMD
    define(["jquery"], factory);
  } else if (typeof exports === "object") {
    // Node, CommonJS之类的
    module.exports = factory(require("jquery"));
  } else {
    // 浏览器全局变量(root 即 window)
    root.returnExports = factory(root.jQuery);
  }
})(this, function ($) {
  //    方法
  function myFunc() {}
  //    暴露公共方法
  return myFunc;
});
```

- AMD
  amd（Asynchronous Module Definitions）规范因 RequireJS 而出名，其适用于浏览器端
  commonjs 常被用于 node 中，当然其也可通过 Browserify 用于浏览器中。
  异步加载
  其被提出，主要用于客户端 browser
  语法不直观，没有 commonjs 便于书写

```js
// 文件名: foo.js
define(["jquery"], function ($) {
  //    方法
  function myFunc() {}
  //    暴露公共方法
  return myFunc;
});
```

- CJS(commonjs)
  语法类似于 node，因为 node 使用 commonjs 规范
  commonjs 导入模块是同步导入
  主要用于后端，客户端用的话需要通过 Browserify
  书写直观

```js
//    暴露公共方法（一个）
function myFunc() {}

module.exports = myFunc;
// 导出方式
module.exports = {
  a: 1,
  b: 2,
};
// 和上面等价，算一种
exports.a = 1;
exports.b = 2;

// 导入方式
const lib = require("./lib");
console.log("a:", lib.a);
console.log("b:", lib.b);
```

- ESM(ES6 Modules)
  1. esm 的浏览器写法

```html
<script type="module" src="./index.js">
  xxx
</script>
```

```js
// 导出方式
export default 'hello world'; // default export
export const name = 'hello world'; // named export
export {name:'hello world'} // 上一种的简便写法

// 导入方式
import lib from './lib'; // default import
import * as lib from './lib';
import { method1, method2 } from './lib';
```

2. 原生 ES 导入不支持下面这样的裸模块导入

```js
import { someMethod } from "my-dep";
```

#### ESM 和 CommonJs 的差异

1. 语法上面，CommonJS 模块使用 require()加载和 module.exports 输出，ES6 模块使用 import 和 export。

2. 用法上面，require()是同步加载，后面的代码必须等待这个命令执行完，才会执行。import 命令则是异步加载，或者更准确地说，ES6 模块有一个独立的静态解析阶段，依赖关系的分析是在那个阶段完成的，最底层的模块第一个执行。

#### esm 和 common 混用

1. CommonJS 模块加载 ES6 模块

```js
(async () => {
  await import("./my-app.mjs");
})();
```

require()不支持 ES6 模块的一个原因是，它是同步加载，而 ES6 模块内部可以使用顶层 await 命令，导致无法被同步加载。

顶层 await 是 es2021 的新特性，目前只有 chrome 浏览器高于 89 版本才支持（2021 年后），及 node15+默认支持无需携带试验参数。在低版本使用还是需要借助 babel 帮忙的。

2. ES6 模块加载 CommonJS 模块

```js
// 正确
import packageMain from "commonjs-package";

// 报错
import { method } from "commonjs-package";
```

ES6 模块需要支持静态代码分析，而 CommonJS 模块的输出接口是 module.exports，是一个对象，无法被静态分析，所以只能整体加载。

3. package.json 指定入口

```js
"exports"：{
    "require": "./index.js"，
    "import": "./esm/wrapper.js"
}
```

在 package.json 文件的 exports 字段，指明两种格式模块各自的加载入口

#### esm 的循环引用问题

esm 的 import 命令是在编译阶段就执行，优先于自身内容执行。
esm 并不关心是否存在循环引用，只是生成一个指向被加载模块的引用，代码未执行时，这个引用的值就是 undefined。

#### tree-shaking

在使用 Tree shaking 的时候，有一个需要特别注意的地方，就是“副作用（side effects）”。

有些模块的代码可能会在导入时执行一些副作用，例如改变全局变量、改变导入模块的状态等。这种情况下，即使模块中的部分导出没有被使用，由于其副作用，也不能被 Tree shaking 移除。否则，可能会导致程序运行出错。

### webpack

#### 魔法注释

```js
/* webpackChunkName: " " */
// 路由懒加载自定义包名，打包的时候路由组件会单独打包出来
/* webpackExclude: /\.def$/ */
/* webpackMode: "lazy" */
/* webpackExports: ["default", "named"] */
```

#### webpack require.ensure

/

```js
import Vue from "vue";
import Router from "vue-router";
// 官网可知：下面没有指定webpackChunkName，每个组件打包成一个js文件。
const Foo = () => import("../components/Foo");
const Aoo = () => import("../components/Aoo");
// 下面2行代码，指定了相同的webpackChunkName，会合并打包成一个js文件。
// const Foo = () => import(/* webpackChunkName: 'ImportFuncDemo' */ '../components/Foo')
// const Aoo = () => import(/* webpackChunkName: 'ImportFuncDemo' */ '../components/Aoo')
export default new Router({
  routes: [
    {
      path: "/Foo",
      name: "Foo",
      component: Foo,
    },
    {
      path: "/Aoo",
      name: "Aoo",
      component: Aoo,
    },
  ],
});
```

require.ensure 可实现按需加载资源，包括 js,css 等。他会给里面 require 的文件单独打包，不会和主文件打包在一起。 有四个入参

- 第一个参数是数组，表明第二个参数里需要依赖的模块，这些会提前加载。
- 第二个是回调函数,在这个回调函数里面 require 的文件会被单独打包成一个 chunk,不会和主文件打包在一起，这样就生成了两个 chunk,第一次加载时只加载主文件。
- 第三个参数是错误回调。
- 第四个参数是单独打包的 chunk 的文件名

import 和 require 的比较（了解）

1. import 是解构过程并且是编译时执行

2. require 是赋值过程并且是运行时才执行，也就是异步加载

3. require 的性能相对于 import 稍低，因为 require 是在运行时才引入模块并且还赋值给某个变量

#### webpack require.context

webpack 构建期间运行的 function,可以实现自动化导入

```js
require.context(directory, useSubdirectories, regExp);
```

```js
const requireApi = require.context(".", true, /\.js$/);
requireApi.keys().forEach((key, index) => {
  if (key === "./index.js") return; // 不将当前文件导入
  Object.assign(module, requireApi(key)); // 将所有导出结果混入当前文件的module对象
});
console.log(module);
console.log(module.b);
```

#### vue-cli webpack 解析

```shell
node --inspect-brk=9229 node_modules/@vue/cli-service/bin/vue-cli-service.js build
```

过程
解析依赖，loader 处理相应文件
loader 里面配 include，让 loader 只针对特殊资源。另一种办法是让你的第三方库 noparse
vue-loader 入手
file-loader 和 url-loader
hook 在 babel-loader 和 vue-loader 之前

webpack 的 loader 和 plugin 的区别
基于 tapable 的插件架构，扩展性强，众多的 loader 或者 plugin 让 webpack 稍显复杂。
在你编写自定义 webpack 扩展前你需要想明白到底是要做一个 loader 还是 plugin 呢？可以这样判断：
如果你的扩展是想对一个个单独的文件进行转换那么就编写 loader 剩下的都是 plugin。
其中对文件进行转换可以是像：
babel-loader 把 es6 转换成 es5
file-loader 把文件替换成对应的 URL
raw-loader 注入文本文件内容到代码里去

#### 编写 webpack loader

请好好利用 loader-utils 包，它提供了很多有用的工具，最常用的一个就是获取传入 loader 的 options。除了 loader-utils 之外包还有 schema-utils 包，我们可以用 schema-utils 提供的工具，获取用于校验 options 的 JSON Schema 常量，从而校验 loader options

我们在函数中处理 source 的转化，最终返回处理好的值。当然返回值的数量和返回方式依据 a-loader 的需求来定。一般情况下可以通过 return 返回一个值，也就是转化后的值。如果需要返回多个参数，则须调用 this.callback(err, values...) 来返回。在异步 loader 中你可以通过抛错来处理异常情况。Webpack 建议我们返回 1 至 2 个参数，第一个参数是转化后的 source，可以是 string 或 buffer。第二个参数可选，是用来当作 SourceMap 的对象。

webpack 规定 use 数组中 loader 的执行顺序是从最后一个到第一个

- 顺序最后的 loader 第一个被调用，它拿到的参数是 source 的内容
- 顺序第一的 loader 最后被调用， webpack 期望它返回 JS 代码，source map 如前面所说是可选的返回值。
- 夹在中间的 loader 被链式调用，他们拿到上个 loader 的返回值，为下一个 loader 提供输入。

```js
    module: {
        rules: [{
            test: /\.html$/,
            use: ['html-loader', {
                loader: 'html-minify-loader',
                options: {
                    comments: false
                }
            }]
        }]
    },
    resolveLoader: {
        // 因为 html-loader 是开源 npm 包，所以这里要添加 'node_modules' 目录
        modules: [path.join(__dirname, './src/loaders'), 'node_modules']
    }
```

```js
var Minimize = require("minimize");
var loaderUtils = require("loader-utils");

module.exports = function (source) {
  var callback = this.async();
  if (this.cacheable) {
    this.cacheable();
  }
  var opts = loaderUtils.getOptions(this) || {};
  var minimize = new Minimize(opts);
  minimize.parse(source, callback);
};
```

#### 编写 webpack plugin

```js
class EndWebpackPlugin {
  constructor(doneCallback, failCallback) {
    this.doneCallback = doneCallback;
    this.failCallback = failCallback;
  }

  apply(compiler) {
    // 监听webpack生命周期里的事件，做相应的处理
    compiler.plugin("done", (stats) => {
      this.doneCallback(stats);
    });
    compiler.plugin("failed", (err) => {
      this.failCallback(err);
    });
  }
}

module.exports = EndWebpackPlugin;
```

loader 的入口需要导出一个 class, 在 new EndWebpackPlugin()的时候通过构造函数传入这个插件需要的参数，在 webpack 启动的时候会先实例化 plugin 再调用 plugin 的 apply 方法，插件需要在 apply 函数里监听 webpack 生命周期里的事件，做相应的处理。
webpack plugin 里有 2 个核心概念：

- Compiler: 从 webpack 启动到推出只存在一个 Compiler，Compiler 存放着 webpack 配置
- Compilation: 由于 webpack 的监听文件变化自动编译机制，Compilation 代表一次编译。
  Compiler 和 Compilation 都会广播一系列事件。
  webpack 生命周期里有非常多的事件可以在[event-hooks](https://webpack.js.org/api/plugins/compiler/#event-hooks)和[Compilation](https://webpack.js.org/api/plugins/compilation/)里查到。以上只是一个最简单的 demo，更复杂的可以查看 [how to write a plugin](https://github.com/webpack/docs/wiki/how-to-write-a-plugin)或参考[web-webpack-plugin](https://github.com/gwuhaolin/web-webpack-plugin)。

#### vue-cli3 下的 webpack

```
用法：vue-cli-service build [options] [entry|pattern]

选项：
  --mode        指定环境模式 (默认值：production)
  --dest        指定输出目录 (默认值：dist)
  --modern      面向现代浏览器带自动回退地构建应用
  --target      app | lib | wc | wc-async (默认值：app)
  --name        库或 Web Components 模式下的名字 (默认值：package.json 中的 "name" 字段或入口文件名)
  --no-clean    在构建项目之前不清除目标目录
  --report      生成 report.html 以帮助分析包内容
  --report-json 生成 report.json 以帮助分析包内容
  --watch       监听文件变化
```

配置输出

```shell
vue inspect>output.js
```

构建包分析

```shell
vue-cli-service build --report
```

#### vue-cli3 下的 webpack 打包优化

- webpack.DllPlugin 方式

  1.  配置 webpack.dll.js 针对第三方库打包
  2.  vue.config.js 中配置 plugin
  3.  html 中引入 dll 打包出来的 js 文件。（一般采用部署 CDN 的方式）

#### webpack 拆包

SplitChunksPlugin
chunks:可以自定义 chunk 输出分组。设置 test 对模块进行过滤，符合条件的模块分配到相同的组
cacheGroups:可以自定义 chunk 输出分组。设置 test 对模块进行过滤，符合条件的模块分配到相同的组
maxInitialRequests(入口的最大并行请求数) 和 maxAsyncRequests(异步请求中的最大并发请求数)

CommonsChunkPlugin

### TurboPack

### vite

#### vite 介绍

[vite 官网](https://github.com/vitejs/vite.git)
[0 配置脚手架示例](https://github.com/JasKang/tsrv.git)

```js
import { createServer, ViteDevServer } from "vite";
```

vite 只是应用于开发、构建、阶段，打包依赖于 rollup 工具
vite 解析实现过程如下:
开发服务器使用 http + connect 构建服务
通过多种中间件处理请求资源；也会遍历 html 节点，将元素的 src 属性添加上 base 前缀，将内联脚本转换为网络请求
会通过 es-module-lexer 这个库分析出所有 import 语句
vite 会在请求后添加 query 参数的方式，用来标识当前请求的处理逻辑

#### vitest

1. toBe 和 toEqual 和 toStrictEqual

对象引用，基本类型用 toBe,会有层级克隆引用问题的 toEqual

#### rollup 插件

- @rollup/plugin-alias 设置别名

```js
// 最后是导出一个插件函数
export default function alias(options: RollupAliasOptions = {})
// 函数返回
return {
    name: 'alias',// 插件名，报错查看
    // inputOptions 对应 rollup.config.js 写的配置 导出的值
    // 构建之前处理
    async buildStart(inputOptions) {
      await Promise.all(
        [...(Array.isArray(options.entries) ? options.entries : []), options].map(
          ({ customResolver }) =>
            customResolver &&
            typeof customResolver === 'object'));
    // 路径映射  a          ../a
    resolveId(importee, importer) {
      // 返回真正的 路径
    }
    }
```

#### esbuild 插件

```js
// build.js

let exampleOnLoadPlugin = {
  name: "example", // 名字为了报错警告提示

  setup(build) {
    let fs = require("fs");
    // 输入的 options
    console.log(build.initialOptions);
    // 我们可以在操作中修改配置  build.initialOptions.outdir = 'lib'

    // build.onStart({}, () => {
    //     // 不能再 onstart 中修改配置
    // })

    // 处理哪类文件
    build.onResolve(
      {
        filter: /\.txt$/,
      },
      async (args) => ({
        path: args.path,
        namespace: "txt", // 区分文件加载，标识
      })
    );

    build.onLoad(
      {
        // 在 go 里执行 正则
        filter: /\.\*$/, //什么类型文件执行
        namespace: "txt",
      },

      async (args) => {
        // 模块的文件路径
        let text = await fs.promises.readFile(args.path, "utf-8");
        return {
          // 文件内容预处理
          contents: `export default ${JSON.stringify([
            ...text.split(/\s+/),
            "----------",
          ])}`,
          // loader: "json",
        };
      }
    );
  },
};

require("esbuild")
  .build({
    // 命令行参数
    entryPoints: ["index.js"],
    bundle: true,
    outdir: "dist",
    loader: {
      ".png": "dataurl",
    },
    plugins: [exampleOnLoadPlugin],
  })
  .catch(() => process.exit(1));
```

#### 通用钩子

开发时，vite dev server 创建一个插件容器按照 rollup 调用创建钩子的规则请求各个钩子函数

1. 构建阶段

服务器启动调用一次

- options(options) 获取操纵 rollup 选项
- buildStart(options) 每次开始构建时调用
- resolveId(source,importer,options)每个传入模块请求时被调用，创建自定义确认函数，，可以用来定位第三方依赖
- load(id) 自定义加载器
- transform(code,id) 转换已加载的模块内容
  服务器关闭调用一次
- buildEnd

2. 输出阶段

- outputOptions(options)：接受输出参数；
- renderStart(outputOptions, inputOptions)：每次 bundle.generate 和 bundle.write 调用时都会被触发；
- augmentChunkHash(chunkInfo)：用来给 chunk 增加 hash；
- renderChunk(code, chunk, options)：转译单个的 chunk 时触发。rollup 输出每一个 chunk 文件的时候都会调用；
- generateBundle(options, bundle, isWrite)：在调用 bundle.write 之前立即触发这个 hook；
- writeBundle(options, bundle)：在调用 bundle.write 后，所有的 chunk 都写入文件后，最后会调用一次 writeBundle；
- closeBundle()：在服务器关闭时被调用

#### vite 特有钩子

config(config,env) 修改 vite 配置
configResolved(resolvedConfig) vite 配置确认
configureServer(server) 用于配置 dev server
transformIndexHtml(html) 转换 index.html 的专用钩子。钩子接收当前的 HTML 字符串和转换上下文
handleHotUpdate(ctx) 执行自定义 HMR 更新，可以通过 ws 往客户端发送自定义的事件

#### 钩子调用顺序

- 配置解析相关
- 构建阶段通用钩子
- 输出阶段的通用钩子

config-->configResolved-->options-->configureServer-->buildStart-->resolvedId-->load-->transform-->buildEnd-->outputOptions-->renderStart-->augmentChunkHash-->renderChunk-->generateBundle-->transformIndexHtml-->writeBundle-->closeBudle

#### 插件执行顺序

- 别名处理 Alias
- 用户插件设置 enforce: 'pre'
- vite 核心插件
- 用户插件未设置 enforce
- vite 构建插件
- 用户插件设置 enforce: 'post'
- vite 构建后置插件(minify, manifest, reporting)

#### vite 插件执行机制

可以通过变量控制 vite 插件执行时机,类似 loader 可以控制执行顺序

```js
// 插件执行是个函数，传参
plugins: [
vue(),
vueJsx(),
testPlugins('post'),
testPlugins(),
testPlugins('pre')
],

// 插件是个函数
export default (enforce?: 'pre' | 'post') => {
  return {
    name: 'test',
    enforce,
    // 启动就调用
    buildStart() {
      console.log('buildstart', enforce)
    },
    // 解析文件时调用，请求时；没有 return， undefined 意思当前插件没有找到相关文件
    // 后续走到了 vite 核心插件中，
    // resolveId() {
    //   console.log('resolveid', enforce) // 都是 pre
    // },
    load() {
      /**
       * 有 pre 和 post
       * 大部分不会再拆件中使用，内部核心插件用，
       */
      console.log('load', enforce)
    },
  }
}
```

#### 热更新 hmr

- 监听 import.meta.hot 和常见钩子

```js
// 可能不存在 hot，  vite build，这段会被 treeshaking
if (import.meta.hot) {
  // 文件可以接受 自己的 热更新,内容改变就会热重载
  import.meta.hot.accept();
  // 只针对第一个参数中依赖的文件改变，才会热更新
  import.meta.hot.accept(["style.css"], (newM) => {
    newM.render(); // 可以手动触发更新
  }); // 只有依赖的文件变了才更新
  // import.meta.hot.accept((newModule) => {
  // newModule.render()
  // });
  // 这里的 timer 会一直存在，热更新完后会存在多个
  let timer = setInterval(() => {
    console.log(8);
  }, 1000);
  // 销毁时触发
  import.meta.hot.dispose(() => {
    // 删除已有的
    if (timer) clearInterval(timer);
  });
  import.meta.hot.invalidate(); // 强制 accept module 之后重新加载应用，页面刷新
  // 热更新数据缓存，避免每次热更新重新开始
  let index = import.meta.hot.data.cache.getIndex() || 0;
  import.meta.hot.data.cache = {
    getIndex() {
      return index;
    },
  };
}
```

#### 参考

(vite 插件)[https://juejin.cn/post/7103165205483356168#heading-16]

### 构建工具

- snowpack
- rollup
- webpack
- gulp 常用 function

```js
import { src, dest, series, parallel } from 'gulp'

const { src, dest } = require('gulp');

function copy() {
  return src('input/*.js')
    .pipe(dest('output/'));
}

exports.copy = copy;

// 将任务函数和/或组合操作组合成更大的操作，这些操作将按顺序依次执行。对于使用 series() 和 parallel() 组合操作的嵌套深度没有强制限制。
/ This is INCORRECT
const { series, parallel } = require('gulp');

const clean = function(cb) {
  // body omitted
  cb();
};

const css = series(clean, function(cb) {
  // body omitted
  cb();
});

const javascript = series(clean, function(cb) {
  // body omitted
  cb();
});

exports.build = parallel(css, javascript);

const { series, parallel } = require('gulp');

function clean(cb) {
  // body omitted
  cb();
}

function css(cb) {
  // body omitted
  cb();
}

function javascript(cb) {
  // body omitted
  cb();
}

exports.build = series(clean, parallel(css, javascript));
```

Rollup 是基于 ES6 实现的代码模块化工具
可以使用 rollup 来编译出符合所有类型规范的代码

```js
// src/rollup.config.js

import { resolve } from "path";
import { name } from "../package.json";

const FORMAT = {
  ES: "es",
  CJS: "cjs",
  UMD: "umd",
};

const base = {
  input: resolve(__dirname, "../src/index.js"),
};

const output = function (format) {
  return {
    name,
    dir: resolve(__dirname, `../dist/${format}`),
    // format 参数，决定输出需要满足哪一种模块化规范
    format,
  };
};

export default [
  {
    ...base,
    output: output(FORMAT.ES),
  },
  {
    ...base,
    output: output(FORMAT.CJS),
  },
  {
    ...base,
    output: output(FORMAT.UMD),
  },
];
```

修改 package.json

```js
{
  "name": "@baidu/hummer-master",
  "version": "1.0.0",
- "main": "src/index.js",
+ "main": "dist/cjs/index.js",
+ "module": "dist/es/index.js",
+ "files": ["dist"],
  "scripts": {
+   "build":"rm -rf ./dist && rollup -c build/rollup.config.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "devDependencies": {
    "rollup": "^2.55.1"
  }
}

```

package:

- rollup-plugin-css-porter

1. 测试 esm 类型

```js
import { method1, method2 } from "./lib";
```

2. 测试 cjs 类型

```js
// mock 一个 nodejs 项目来测试 cjs 规范
// index.js
const { HummerMaster } = require("@baidu/hummer-master");
const hummerMaster = new HummerMaster();
console.log(hummerMaster.getStory());

console.log("Hello World!");
```

3. 测试 umd 类型

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
  </head>
  <body>
    <script src="./node_modules/@baidu/hummer-master/dist/umd/index.js"></script>
    <script>
      const { HummerMaster } = window["@baidu/hummer-master"];
      const hummerMaster = new HummerMaster();
      console.log(hummerMaster.getStory());
      console.log("Hello World!");
    </script>
  </body>
</html>
```

### cli 工具开发

1. #! /usr/bin/env node

执行文件 bin/cli.js 里要加上这句

意思就是用 node 来执行此文件，node 怎么来呢，就去用户(usr)的安装根目录(bin)下的 env 环境变量中去找，简单的说就是如果在 windows 上面，就去安装 node 的 bin 目录去找 node 执行器，一般我们都放在环境变量中，所以就能正确找到 node 来执行

2. package.json 的 bin 字段

bin 字段是命令名到本地文件名的映射。在安装时，npm 会将文件符号链接到 prefix/bin 以进行全局安装或./node_modules/.bin/本地安装。
当我们使用 npm 或者 yarn 命令安装包时，如果该包的 package.json 文件有 bin 字段，就会在 node_modules 文件夹下面的 .bin 目录中复制了 bin 字段链接的执行文件。我们在调用执行文件时，可以不带路径，直接使用命令名来执行相对应的执行文件。
