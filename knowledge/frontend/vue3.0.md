---
title: vue3.0
date: 2019-09-21 11:23:51
categories: frontend #文章分类
tags: [vue3.0] #文章标签，可以一次添加多个标签
---

vue3.0

<!-- more -->

### 开发工具

- (新的友好的支持 vue3 的 vscode 开发插件) volar
- (官方脚手架工具 create-vue)[https://github.com/vuejs/create-vue]
- (Vitesse)[https://github.com/antfu/vitesse]
- (vue3-eslint-stylelint-demo) [https://github.com/sethidden/vue3-eslint-stylelint-demo](Volar + ESLint + stylelint + husky)
- (volar-starter)[https://github.com/johnsoncodehk/volar-starter] (For bug report and experiment features testing)

### 生命周期

destroyed 生命周期选项被重命名为 unmounted
beforeDestroy 生命周期选项被重命名为 beforeUnmount

### Composition API 简单使用

#### teleport 组件

有时组件模板的一部分逻辑上属于该组件，而从技术角度来看，最好将模板的这一部分移动到 DOM 中 Vue app 之外的其他位置。

Teleport 提供了一种干净的方法，允许我们控制在 DOM 中哪个父节点下渲染了 HTML，而不必求助于全局状态或将其拆分为两个组件

```XML
<teleport to="#modals">
  <div>A</div>
</teleport>
<teleport to="#modals">
  <div>B</div>
</teleport>

<!-- result-->
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```

### stylelint

StyleLint 是『一个强大的、现代化的 CSS 检测工具』, 与 ESLint 类似, 是通过定义一系列的编码风格规则帮助我们避免在样式表中出现错误.
(StyleLint 使用指南)[https://www.cnblogs.com/jiaoshou/p/11284204.html]

#### setup 的形参

```js
<script setup></script>
```

采用 script setup 语法糖的话这种方式就不可行了，原因是它会自动以文件名为主，不需要在写 name 属性
需要设置 name 直接在 script setup 同级中再添加一个 script 即可。

```js
<script>
    export default {
      name: 'xxx',
    };
</script>

<script setup>
</script>
```

如果同时使用了 typescript 的话，可以配合插件直接在 script 标签中设置 name，具体方式如下：

安装插件：vite-plugin-vue-setup-extend

```js
<script lang="ts" setup name="xxx"></script>
```

#### `reactive()` 和 `ref()`的比对

1. ref() 单独地为某个数据提供响应式能力

想要保持对象内容的响应式能力，在 return 的时候必须把整个 reactive() 对象返回出去，同时在引用的时候也必须对整个对象进行引用而无法解构，否则这个对象内容的响应式能力将会丢失。

2. reactive() 给一整个对象赋予响应式能力

在具体的业务中，如果无法使用解构取出 reactive() 对象的值，每次都需要通过 . 操作符访问它里面的属性会是非常麻烦的，所以官方提供了 toRefs() 函数来为我们填好这个坑。只要使用 toRefs() 把 reactive() 对象包装一下，就能够通过解构单独使用它里面的内容了，而此时的内容也依然维持着响应式的特性。

3. ref可以包装基本类型和引用类型，reactive只能包装引用类型。第二点，ref取值要用.value，而reactive可以直接访问

4. ref解构后仍然保持响应性，因为解构的是 ref 对象。而reactive解构后会丢失响应性，因为解构的是普通对象。如果需要解构并保持响应性，可以使用 toRefs。

3. 如何选择 ref 和 reactive？

- 优先 ref

* 管理基本类型数据
* 需要明确的数据引用（如传递到函数中仍保持响应性）

- 优先 reactive

* 管理复杂对象/数组，避免频繁使用.value
* 需要深层嵌套的响应式数据

```js
 setup() {
    const state: any = reactive({
      menuList: []
    })
    return {
    ...toRefs(state)
    }
 }
```

#### isRef,unRef,toRef 和 toRefs

```js
let foo: unknown;
if (isRef(foo)) {
  // foo 的类型被收窄为了 Ref<unknown>
  foo.value;
}
function useFoo(x: number | Ref<number>) {
  const unwrapped = unref(x);
  // unwrapped 现在保证为 number 类型
}
const state = reactive({
  foo: 1,
  bar: 2,
});

const fooRef = toRef(state, "foo");

// 更改该 ref 会更新源属性
fooRef.value++;
console.log(state.foo); // 2

const state = reactive({
  foo: 1,
  bar: 2,
});

const stateAsRefs = toRefs(state);
/*
stateAsRefs 的类型：{
  foo: Ref<number>,
  bar: Ref<number>
}
*/

function useFeatureX() {
  const state = reactive({
    foo: 1,
    bar: 2,
  });

  // ...基于状态的操作逻辑

  // 在返回时都转为 ref
  return toRefs(state);
}

// 可以解构而不会失去响应性
const { foo, bar } = useFeatureX();
```

#### ref、reactive、shallowRef、 shallowReactive、toRaw、unref、toRef、toRefs、customRef

shallowRef & shallowReactive 只对对象的第一层做监听变化

customRef 创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。它需要一个工厂函数，该函数接收 track 和 trigger 函数作为参数，并且应该返回一个带有 get 和 set 的对象

```js
function useDebouncedRef(value, delay = 200) {
  let timeout;
  return customRef((track, trigger) => {
    return {
      get() {
        track();
        return value;
      },
      set(newValue) {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
          value = newValue;
          trigger();
        }, delay);
      },
    };
  });
}

export default {
  setup() {
    return {
      text: useDebouncedRef("hello"),
    };
  },
};
```

#### 定义 prop emit

1. defineProps

```ts
import { defineProps } from "vue";
// 类型定义
interface UserInfo {
  id: number;
  age: number;
}

defineProps<{
  name: string;
  userInfo: UserInfo;
}>();
```

2. defineEmits

```ts
import { defineEmits } from "vue";
```

子组件

```vue
<template>
  <button @click="triggerFatherAdd">点击触发父组件add</button>
</template>
<script setup lang="ts">
import { defineEmits } from "vue";

// 子组件使用defineEmits向父组件抛出事件
const emits = defineEmits(["add", "update"]); //事件数组

// 触发调用子组件时的自定义事件add
const triggerFatherAdd = () => {
  emits("add", "新增数据"); //后面是参数
};
</script>
```

父组件

```vue
<template>
  <myson @add="add"></myson>
</template>

<script setup lang="ts">
import myson from "./myson.vue";
const add = (msg: string) => {
  console.log("add", msg);
};
</script>
```

3. defineExpose
   script setup 默认是不对外界暴露组件实例的，所以在其他组件中通过诸如$refs和$parent 都默认无法获取当前组件实例，但是通过 defineExpose 暴露的可以获取。

显示暴露的数据，才可以在父组件拿到

```vue
<script setup lang="ts">
import { defineExpose } from "vue";

// 定义一个想提供给父组件拿到的数据
const msg: string = "Hello World!";

// 显示暴露的数据，才可以在父组件拿到
defineExpose({
  msg,
});
</script>
```

#### 使用 slots 和 attrs

- 通过 useSlots 和 useAttrs 来定义，达到类似$slots和$attrs 的效果

```js
// 导入 useAttrs 组件
import { useAttrs } from "vue";
// 获取 attrs
const attrs = useAttrs();
// attrs是个对象，和 props 一样，需要通过 key 来得到对应的单个 attr
console.log(attrs.msg);
```

#### 使用 provide 给子孙组件传值和 inject 接收父组件的值

父组件

```vue
<script setup lang="ts">
import { provide } from "vue";
const curUserId = 168; //可以是简单类型，也可以是复杂类型
provide("curUserId", curUserId);
</script>
```

孙子组件

```vue
<script setup lang="ts">
import { inject } from "vue";
const curUserId = inject("curUserId");
</script>
```

#### watch 和 watchEffect

1. watch ()

第一个参数为观察 ref 物件，第二个参数为一个 callBack ，当状态更新，就会针对其来执行 callback。

2. watchEffect()

**不用指定要监听的目标**，只要在 callback 函式中对应响应式资料更新后就会依照对应资料来执行了，而与 watch 不同的是，watchEffect () **在初始 setup () 的时候，就会先执行一次了**。
第一个参数还可以是队列，如果是 reative 中的一个 key-value,用有回传值的 getter 函式，即()=>state.key，深度监听在第三个参数{deep: true}

#### 属性透传

```
<!-- 父组件 -->
<Child class="child-style" data-id="123" />

<!-- 子组件 -->
<div v-bind="$attrs"></div>
<script setup>
// 禁用自动继承
defineOptions({ inheritAttrs: false });
</script>
```

### v-model 的参数分析

v-model 其实是做双向绑定，实际上是语法糖

#### vue2.x.x 的语法

```js

// 1.单个prop实现数据的双向绑定 basic
<ChildComponent v-model="pageTitle" />
// would be shorthand for:
<ChildComponent :value="pageTitle" @input="pageTitle = $event" />

// 2. 不想用value做一个prop name的情况
// 双向绑定的变量名从value灵活成model
// ChildComponent.vue
export default {
  model: {
    prop: 'title',
    event: 'change'
  },
  props: {
    // this allows using the `value` prop for a different purpose
    value: String,
    // use `title` as the prop which take the place of `value`
    title: {
      type: String,
      default: 'Default title'
    }
  }
}
// would be shorthand for:
<ChildComponent :title="pageTitle" @change="pageTitle = $event" />

// would be shorthand for:
<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />

// would be shorthand for:
<ChildComponent :title.sync="pageTitle" />

//3. 多个prop实现数据的双向绑定 Using v-bind.sync
this.$emit('update:title', newValue)

```

#### vue3.x.x 的语法

- 2.x 的 v-model 只能有一个,3.x 灵活为支持多个

```html
<ChildComponent v-model:title="pageTitle" v-model:content="pageContent" />
```

- 除了像 .trim 这样的 2.x 硬编码的 v-model 修饰符外，现在 3.x 还支持自定义修饰符

```html
<ChildComponent v-model.capitalize="pageTitle" />
```

```html
<!-- 1 -->

<ChildComponent v-model="pageTitle" />

<!-- would be shorthand for: -->

<ChildComponent
  :modelValue="pageTitle"
  @update:modelValue="pageTitle = $event"
/>

<!-- 2 -->
<!-- props双向绑定的新写法 -->
<ChildComponent v-model:title="pageTitle" />

<!-- would be shorthand for: -->

<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
```

#### Vue3 指令新特性

1. 支持多个 v-model
2. 自定义修饰符：通过 modelModifiers 访问
3. 作用域插槽

4. 动态属性名

```
<img :src="imageUrl" :alt="altText">
<!-- 动态属性名 -->
<div :[dynamicAttr]="value"></div>
```

### 进阶

组合式 API 中组件的 setup() 钩子可以是异步的
如果使用 <script setup>，那么顶层 await 表达式会自动让该组件成为一个异步依赖
<Suspense> 组件有两个插槽：#default 和 #fallback。两个插槽都只允许一个直接子节点。在可能的时候都将显示默认插槽中的节点。否则将显示后备插槽中的节点。

### VueUse

为 Vue 2 和 3 服务的一套 Vue Composition API 的常用工具集，是目前世界上 Star 最高的同类型库之一。它的初衷就是将一切原本并不支持响应式的 JS API 变得支持响应式，省去程序员自己写相关代码。
(官网)[https://vueuse.org/guide/index.html]

#### 实现细节

vue.install

### 资料

[掘金 vue3 资源](https://juejin.cn/post/7022137486407106591#heading-9)

### ts 相关支持

#### 实现注入到 vue 实例上的声明定义

```typescript
class PrototypeUtils {
  static install() {
    Vue.prototype.functionName = function (apple = "helloworld") {
      console.log(apple);
    };
  }
}
Vue.use(PrototypeUtils);
// 全局的d.ts文件
declare module "vue/types/vue" {
  interface Vue {
    functionName: (apple: string) => void;
  }
}
```

### vuex

Pinia API

- 没有 mutations,用$patch 代替
- 更好 typescript 支持
- 没有命名空间模块

1. 定义

```js
// store/user.js中定义具体的store
export const UserStore =  defineStore('user', {

state:()=>({
    name:'',
    id:''
})
getters:{
nameId:state=>`${state.name}_${state.id}`
}
actions:{
    async getUserInfo(){}
}
})

//store/index.js
//这样写的好处是，以后引用的时候可以直接from '@/store'，并且当文件多了，可以用通过webpack的require.context或者vite的import blob来自动处理
export {UserStore} from './user'

```

2. 使用

```js
import UserStore from "@/store";
const user = UserStore();
user.name = "test";
//or
user.$patch({
  name: "test",
  id: 123,
});
//or
user.$patch((state) => {
  state.name = "test";
  state.id = 123;
});
```

### style v-bind

css 变量的 hack 结合 vue

```js
<template>
<p>123</p>
</template>
<script setup>
const pcolor = ref('#000')
</script>
<style scoped>
p{
 color:v-bind('pcolor')
}
</style>

```

#### 拓展(原生 css 变量)

1. 变量声明

```css
body {
  --foo: #7f583f;
  --bar: #f7efd2;
}
```

为什么选择两根连词线（--）表示变量？因为$foo 被 Sass 用掉了，@foo 被 Less 用掉了。为了不产生冲突，官方的 CSS 变量就改用两根连词线了。 2. 变量应用 2. var 变量

```css
a {
  color: var(--foo);
  text-decoration-color: var(--bar);
  /* 使用第二个参数，表示变量的默认值。如果该变量不存在，就会使用这个默认值。 */
  color: var(--foo, #7f583f);
}
```

3. 同一个 CSS 变量，可以在多个选择器内声明。读取的时候，优先级最高的声明生效

4. JavaScript 操作 CSS 变量

```js
// 设置变量
document.body.style.setProperty("--primary", "#7F583F");

// 读取变量
document.body.style.getPropertyValue("--primary").trim();
// '#7F583F'

// 删除变量
document.body.style.removeProperty("--primary");
```

### 常用 tips

1. vue3 的路由懒加载

```ts
// 3.x
import { defineAsyncComponent } from "vue";
component: defineAsyncComponent(() => import(`/src/${view}.vue`));
// 2.x
component: () => import(`/src/${view}.vue`);
```

2. vite&webpack 动态加载

```ts
// vite 动态添加模块
const modulesFiles = import.meta.globEager("./modules/*.js");
//webpack动态添加模块
const modulesFiles = require.context("./modules", true, /\.js$/);
```

3. vite 中使用批量导入 import.meta.glob 和 import.meta.globEager 区别

- import.meta.glob 为过动态导入，构建时，会分离为独立的 chunk
- import.meta.globEager 为直接引入
- (glob-import)[https://cn.vitejs.dev/guide/features.html#glob-import]
- (pattern-syntax)[https://github.com/mrmlnc/fast-glob#pattern-syntax]

import.meta.globEager **已弃用**,用 **import.meta.glob('\_', { eager: true })** 来代替。

4. vite 预构建缓存问题

Vite 有个预构建阶段，用于将 commonjs/UMD 模块转为 ESM，和合并多个模块。就是把一些模块处理后放在 node_modules/.vite/deps 目录下，项目启动时直接引用这个目录下的内容。
值得注意的是，这一阶段是有缓存的，且存在**两处缓存**，一处是.vite/deps 下的缓存，一处是浏览器的缓存。如果发现修改了插件，但是观察不到效果，可以尝试 **npx vite --fore**，以及禁用浏览器缓存。

5. vue 和 typescript

ts-loader：让 webpack 能够识别 ts 文件

由于 TypeScript 默认并不支持 \*.vue 后缀的文件，所以在 vue 项目中引入的时候需要创建一个 vue-shim.d.ts 文件，放在根目录下

```ts
declare module "*.vue" {
  import Vue from "vue";
  export default Vue;
}
```

6. v-slot:xxx 的语法糖#xxx
   jsx/tsx 中如何使用插槽
   [jsx 插槽](https://github.com/vuejs/jsx-next/blob/dev/packages/babel-plugin-jsx/README-zh_CN.md#插槽)

7. webpack 和 Vite 环境变量区别
   webpack,vite 配置.env。test,.env.development 文件和.env.production 文件
   script 中可以通过添加--mode development 指定环境变量
   webpack 可以在项目中通过 process.env.[name]来访问我们定义的变量
   vite 则是通过 import.meta.env.[name]来访问
   Vite 为了防止意外地将一些环境变量泄漏到客户端，只有以 VITE\_ 为前缀的变量才会暴露给经过 vite 处理的代码
   vue 的 webpack 当我们配置环境变量时候，除了 process.env 已有的 baseUrl 和 NODE_ENV 可以更改值，对于我们想要自定义添加的环境变量必须使用 VUE_APP 开头。

```shell
npm run build:test # webpack
npm run build --mode test
```

```js
import { defineConfig, loadEnv } from "vite";
VITE_BASE_API = "http://api.admin.gumingchen.icu";
const url = import.meta.env.VITE_BASE_API + "/requestApi"; // Vite
export default defineConfig(({ mode }) => {
  const env = loadEnv(mode, process.cwd(), "VITE_");
  const url = BASE_API;
  return {};
});
```
