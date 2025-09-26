---
title: vue基础
date: 2017-02-21 11:23:51
categories: frontend #文章分类
tags: [vue基础] #文章标签，可以一次添加多个标签
---

vue 基础...

<!-- more -->

### 更新安装 vue 脚手架

vue-cli 中集成了一个小型的 express,都出到 5 了

```shell
	npm install --global @vue/cli
```

#### 打包模式

应用模式是默认的模式。在这个模式中：

- index.html 会带有注入的资源和 resource hint
- 第三方库会被分到一个独立包以便更好的缓存
- 小于 8KiB 的静态资源会被内联在 JavaScript 中
- public 中的静态资源会被复制到输出目录中

#### 打包优化

1. 懒加载
2. 不常改变的库在 index.html 上用 cdn 引入
3. webpack.DllPlugin
4. SplitChunksPlugin
5. CommonsChunkPlugin

- 差量包???
  通过对 output.filename 和 output.chunkFilename 的配置，利用[contenthash]占位符，为 js 文件名加上根据其内容生成的唯一 hash 值，轻松实现资源的长效缓存。也就是说，无论是第几次打包，内容没有变化的资源 (如 js、css) 文件名永远不会变，而那些有修改的文件就会生成新的文件名 (hash 值) 。

因为 webpack 内部维护了一个自增的数字 id，每个 module 都有一个 id。当增加或删除 module 的时候，id 就会变化，导致其它 module 虽然内容没有变化，但由于 id 被强占，只能自增或者自减，导致整个项目的 module id 的顺序都错乱了。
也就是说，如果引入了一个新模块或删掉一个模块，都可能导致其它文件的 moduleId 发生改变，相应地文件内容也就改变，缓存便失效了。
同样地，chunk 的新增/减少也会导致 chunk id 顺序发生错乱，那么原本的缓存就不作数了。

#### 问题 1：vue 过渡钩子函数实现动画效果

```js
  <transition
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
    v-bind:css="false">
    <p v-if="show">
      Imgss
    </p>
  </transition>
  vue实现methods
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
      el.style.transformOrigin = '50% 50%'
    },
    enter: function (el, done) {
      Velocity(el, { opacity: 1,translateX:'20px', fontSize: '2em' }, { duration: 300 });
      Velocity(el, { translateX:'0px',fontSize: '1em' }, { complete: done })
    },
    leave: function (el, done) {
      Velocity(el, { rotateZ: '1080deg',scale:'0.1' ,opacity: 0},{duration:3000}, { complete: done });
    }
  }
```

#### 问题 2: vue-router 的钩子函数

1. 全局路由钩子

```js
router.beforeEach((to, from, next) => {
  //会在任意路由跳转前执行，next一定要记着执行，不然路由不能跳转了
  console.log("beforeEach");
  console.log(to, from);
  //
  next();
});
//
router.afterEach((to, from) => {
  //会在任意路由跳转后执行
  console.log("afterEach");
});
```

2. 单个路由钩子

```js
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
```

#### 问题 3: vue 的 method,watch 和 computed 的区别

    三者都是函数型驱动，，methods不处理数据逻辑关系，只提供可调用的函数,,是需要主动调用的..
    watch和computed能够“自动执行”预先定义的函数

#### 问题 4: vue 的 prop 的类型支持联合声明??

```js
props:{
    time: {
      type: [Number, String],
      default: () => 0
    }
}
```

#### 问题 5: provide/inject 替换 eventBus 实现祖孙通信

```js
// 父级组件提供 'foo'
var Provider = {
  provide: {
    foo: "bar",
  },
  // ...
};

// 子组件注入 'foo'
var Child = {
  inject: ["foo"],
  created() {
    console.log(this.foo); // => "bar"
  },
  // ...
};
```

```js
// watch和computed的区别
computed:{
  fullName: function () {
    return this.firstName + this.lastName
  }
}
watch: { firstName: function (val) { this.fullName = val + this.lastName } }
new Vue({
  el: '#app',
  data: { fullName: '彭湖湾', firstName: '彭', secName: '湖',thirdName: '湾' },
  // watch中的代码显然是同类型，重复的，它并不简洁，也不优雅
  watch: {
    firstName: function (newValue) {
      this.fullName = newValue + this.secName + this.thirdName
      console.log(this.fullName)
    },
    secName: function (newValue) {
      this.fullName = this.firstName + newValue + this.thirdName
      console.log(this.fullName)
    },
    thirdName: function (newValue) {
      this.fullName = this.firstName + this.secName + newValue
      console.log(this.fullName)
    }
} })
```

    数据触发条件不同，watch是firstName改变时触发fullName做出相应的更改
    				  computed是firstname和latName改变时触发fullName的改变
    所以两者处理的场景不太一样
    				computed擅长处理的场景：一个数据受多个数据影响
    				watch擅长处理的场景：一个数据影响多个数据

    method和computed
    	1.存在依赖型数据 2.依赖型数据发生改变这两个条件,computed才会重新计算。
    	2.methods下的数据，是每次触发都会进行计算的

#### @hook:mounted="doSomething"

父组件引用子组件的时候通过@hook 来监听

#### 组件中的 data 为什么是个 function

因为组件存在复用的情况，且 JS 里对象是引用关系，如果组件中 data 是一个对象，那么这样作用域没有隔离，子组件中的 data 属性值会相互影响，如果组件中 data 选项是一个函数，那么每个实例可以维护一份被返回对象的独立的拷贝，组件实例之间的 data 属性值不会互相影响；而 new Vue 的实例，是不会被复用的，因此不存在引用对象的问题。

#### 组件嵌套

组件的嵌套，solt 实现组件嵌套和直接用标签名标记嵌套，solt 可以实现组件的多态
slot 就是外部调用时，标签中的内容。如果外部调用时没有提供内容的话，那么它就会使用自己默认提供的内容，非常方便

#### vue 的路由和 express 的路由的区别

占坑

#### index 做为 v-for 的 key

使用 index 做 key，破坏顺序操作的时候， 因为每一个节点都找不到对应的 key，导致部分节点不能复用,所有的新 vnode 都需要重新创建。

解决方案:

1. 在开发中最好每条数据使用唯一标识固定的数据作为 key，比如后台返回的 ID，手机号，身份证号等唯一值
2. 可以采用 Symbol 作为 key，Symbol 是 ES6 引入了一种新的原始数据类型 Symbol ，表示独一无二的值，最大的用法是用来定义对象的唯一属性名。
3. 可以采用 uuid 作为 key ，uuid 是 Universally Unique Identifier 的缩写，它是在一定的范围内（从特定的名字空间到全球）唯一的机器生成的标识符

4. 在某些情况下用 index 作为 key 进行强制重用, 可以极大的优化性能.. 比如说虚拟列表, 如果是 key 每次都不一样的话, 就需要反复创建销毁, 但强制重用的话就只需要替换 data 然后重渲染就行了
5. 如果不对数据进行逆序添加 逆序删除破坏顺序的操作， 只用于列表展示的话 使用 index 作为 Key 没有毛病

### vue-router

vue-router 默认的 hash 模式和 history 模式的区别

1. hash模式

#后面的部分称为哈希值，它不会发送到服务器，而是用于在客户端内部进行路由导航

当用户点击链接或在浏览器中前进后退时，哈希值会改变，但浏览器不会重载，而是通过javascript来更新url的hash部分，并根据哈希值来渲染对应的组件



2. history模式

用 history 模式没有#,会和后台的接口耦合在一起
这种模式利用了 HTML5 的 History API，通过 pushState 和 replaceState 方法来改变 URL，同时更新浏览器的历史记录

服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面
这么做以后，你的服务器就不再返回 404 错误页面，因为对于所有路径都会返回 index.html 文件。为了避免这种情况，你**应该在 Vue 应用里面覆盖所有的路由情况，然后在给出一个 404 页面**。

缺点需要服务器配置，以确保用户在直接访问某个url时，，服务器能正确返回资源

### vuex

专为 Vue.js 应用程序开发的状态管理模式
组件的共享状态抽取出来,以一个全局单例模式管理, 能在全局往所有的组件注入数据

single 组件可以抽出来，多个不同组件用同一个数据源(集中式状态管理)
SPA（单页应用程序）
vuex：规范组件中数据流动，主要用于异步的 http 请求后数据的刷新。通过官方的 vue-devtools 可以无缝对接

- state:
- mutations: mutation 必须同步执行
- getters: 在获取数据之前进行的一种再编辑,相当于对数据的一个过滤和加工
- actions: 可以异步执行
- module:
  1.  由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。
  2.  为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割

dispatch --> actions
commit --> mutations

Getter 就是把组件中共有的对 state 的操作进行了提取，它就相当于 对 state 的 computed. 所以它会获得 state 作为第一个参数。(也可以不用 getter，那么在每個組件需要的 state 的加工后操作返回值時，都需要在各自的 computed 中进行处理，这不就重复么)

### 常见常用点

1. 如果是得到 state 还需要进一步操作 filter，在 getter 中完成，，减少 view 或者是 component 的代码量

2. mutation 保证是纯函数，，在 action 中用 commit 的方式去添加处理逻辑

3. keepAlive 很轻松的就能够实现组件之间的缓存，主要还是返回其 DOM 不让数据重新刷新。当使用了 keepAlive 之后，页面第一次进入，钩子的触发顺序 created->mounted->activated，退出时触发 deactivated。再次进入（前进或者后退）时，只触发 actived。

4. vuex 表单字段映射工具 vuex-map-fields
5. vm.\$emit( event, […args] )
   参数：{string} event[...args]
   触发当前实例上的事件。附加参数都会传给监听器回调。
   例子：可以实现在子组件的实例中调用父组件的方法

### vue 性能优化

JSON 数据规范化（normalize）
数据持久化， vuex-persistedstate
如果你的应用存在非常长或者无限滚动的列表，那么采用 窗口化 的技术来优化性能，只需要渲染少部分区域的内容，减少重新渲染组件和创建 dom 节点的时间。
vue-virtual-scroll-list 和 vue-virtual-scroller
可以参考 Google 工程师的文章 Complexities of an Infinite Scroller 来尝试自己实现一个虚拟的滚动列表来优化性能，
主要使用到的技术是 DOM 回收、墓碑元素和滚动锚定。
组件懒加载(异步组件)

#### vue 钩子函数

beforeCreate
created
beforeMount
mounted
**beforeUpdate**
**updated**
keep-alive
actived(keep-alive 组件激活时调用)
deactivated(keep-alive 组件停用时调用)
errorCaptured(err: Error, vm: Component, info: string) => ?boolean 2.5.0 新增
<keep-alive> 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们
当组件在 <keep-alive> 内被切换，它的 activated 和 deactivated 这两个生命周期钩子函数将会被对应执行。

#### 在哪个生命周期内调用异步请求？

可以在钩子函数 created、beforeMount、mounted 中进行调用，因为在这三个钩子函数中，data 已经创建，可以将服务端端返回的数据进行赋值。推荐在 created 钩子函数中调用异步请求，因为在 **created** 钩子函数中调用异步请求有以下优点：

- 能更快获取到服务端数据，减少页面  loading 时间；
- ssr  不支持 beforeMount 、mounted 钩子函数，所以放在 created 中有助于一致性；

#### 在 mounted 中可以访问操作 DOM

#### vue extends 和 mixin 的区别

    extends继承单组件
    minxin可以继承数组
    让子类拥有一些通用成员变量，然而子类也可以重写父类的成员变量。这样的整个编程思想就很面向对象，也就是继承性
    结论: 优先调用mixins和extends继承的父类，extends触发的优先级更高，相对于是队列

#### 对象方法优化

Object.defineProperty (getter/setter,v-model 原理)

1. proxy 有多达 13 种拦截方法，不限于 apply、ownKeys、deleteProperty、has 等是 object.defineProperty 不具备的。
2. proxy 可以**直接监听数组的变化**，proxy 可以**直接监听对象而非属性**。
   vue2.0 中，比如 vm.arr.length–或者 vm.arr[0]=100 均不能触发视图更新；
   如果想更新索引可以使用

```js
vm.$set(vm.arr, 0, 100);
vm.$delete(vm.arr, 0);
```

3. **proxy 返回的是一个新对象，我们可以只操作新对象达到目的，不需要深度遍历监听，性能高于 Object.defineProperty；而 Object.defineProperty 只能遍历对象属性直接修改**

Object.freeze()
Object.freeze() 会把对象冻结，所以比较适合展示类的场景，
如果你的数据属性需要改变，可以重新替换成一个新的 Object.freeze()的对象。
判断空对象
Object.keys({}).length === 0

4. Object.defineProperty 只能**劫持对象的属性**,因此我们需要对每个对象的每个属性进行遍历。Vue 2.x 里，是通过 递归 + 遍历 data 对象来实现对数据的监控的，如果属性值也是对象那么需要深度遍历,显然如果能劫持一个完整的对象是才是更好的选择。所以 vue3 用了 proxy

#### vm.\$nextTick( [callback] )使用场景

    渲染更新视图后的dom结构的获取调用方法

示例场景
1、点击按钮显示原本以 v-show = false 隐藏起来的输入框，并获取焦点。

```js
showsou(){
this.showit = true
this.\$nextTick(function () {
// DOM 更新了
document.getElementById("keywords").focus()
})
}
```

#### 组件的嵌套

solt 实现组件嵌套和直接用标签名标记嵌套
solt 可以实现组件的多态
slot 就是外部调用时，标签中的内容。如果外部调用时没有提供内容的话，那么它就会使用自己默认提供的内容，非常方便

#### Vue 的过滤器

2.0 中已经废弃了过滤器，需要我们自定义

过滤器现在只能用在两个地方：mustache 插值和 v-bind 表达式。在 v-model 、v-on 、v-for 等指令时 Vue 还是推荐我们将该逻辑通过 computed 来计算属性。

v-html 和 v-text 实现内容插入，动态绑定
template 花括号也支持表达式，但不能 var 和 if，有时可用三目运算符实现 if 运算

v-for 和 v-bind:key
当我们在使用 v-for 进行渲染时，尽可能使用渲染元素自身属性的 id 给渲染的元素绑定一个 key 值，这样在当前渲染元素的 DOM 结构发生变化时，能够单独响应该元素而不触发所有元素的渲染。

#### jest

[Vue Test Utils 官方文档](https://vue-test-utils.vuejs.org/zh/api/shallowMount.html)

1. @vue/cli-plugin-unit-jest

```bash
cd vue-jest-demo
vue add @vue/cli-plugin-unit-jest

yarn add vue-jest
yarn test:unit

```

2. 使用@vue/test-utils

- Vue Test Utils 是 Vue.js 官方的单元测试实用工具库，更多语法
- describe: 定义一个测试集
- it: 每个 it 包含单元测试的最小集
- shallowMount：浅渲染，只渲染 HelloWorld 组件，里面的子组件不渲染
- mount: 正常的渲染，子组件也会渲染
- npm run test -- --watchAll, 加了 watchAll 参数，单元测试代码保存的时候会自动运行

3. cli

```bash
vue add unit-jest
```

4. Jest 单元测试的几个指标

%stmts 是语句覆盖率（statement coverage）：是不是每个语句都执行了？
%Branch 分支覆盖率（branch coverage）：是不是每个 if 代码块都执行了？
%Funcs 函数覆盖率（function coverage）：是不是每个函数都调用了？
%Lines 行覆盖率（line coverage）：是不是每一行都执行了？

5. jest 单元测试术语
   describe 描述, decribe 会形成一个作用域
   it 断言
   expect 期望
   test 测试，类似 it

```js
describe("number test", () => {
  it("1 is true", () => {
    expect(1).toBeTruthy();
  });
  test("2 is true", () => {
    expect(2).toBeTruthy();
  });
});
```

```js
import Vue from "vue";
import HelloWorld from "@/components/HelloWorld.vue";

describe("HelloWorld.vue", () => {
  it("renders props.msg when passed", () => {
    const root = document.createElement("div");
    root.className = "root";
    document.body.appendChild(root);
    new Vue({
      render: (h) =>
        h(HelloWorld, {
          props: {
            msg: "dell lee",
          },
        }),
    }).$mount(".root");
    expect(document.getElementsByClassName("hello").length).toBe(1);
  });
});
```

```js
import { shallowMount } from "@vue/test-utils";
import HelloWorld from "@/components/HelloWorld.vue";

it("renders props.msg when passed", () => {
  const msg = "dell lee";
  const wrapper = shallowMount(HelloWorld, {
    propsData: { msg },
  });
  expect(wrapper.props("msg")).toEqual(msg);
  expect(wrapper.text()).toMatch(msg);
  expect(wrapper.findAll(".mm").length).toBe(1);
  expect(wrapper.setProps({ msg: "hello" }));
  expect(wrapper.props("msg")).toEqual("hello");
});

it("组件渲染正常", () => {
  const wrapper = shallowMount(HelloWorld, {
    propsData: { msg: "dell lee" },
  });
  expect(wrapper).toMatchSnapshot();
});
```

Sinon 是用来辅助我们进行前端测试的，在我们的代码需要与其他系统或者函数对接时，它可以模拟这些场景，从而使我们测试的时候不再依赖这些场景。

#### elementui 自定义指令

clickoutside 是 Element-ui 实现的一个自定义指令，顾名思义，该指令用来处理目标节点之外的点击事件，常用来处理下拉菜单等展开内容的关闭，在 Element-ui 的 Select 选择器、Dropdown 下拉菜单、Popover 弹出框等组件中都用到了该指令，所以这个指令在实现一些自定义组件的时候非常有用。

#### babel-plugin-component

借助 babel-plugin-component，我们可以只引入需要的组件，以达到减小项目体积的目的。

### 资料

[Vue 项目性能优化 — 实践指南](https://juejin.cn/post/6844903913410314247#heading-26)
