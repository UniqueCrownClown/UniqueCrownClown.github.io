---
title: vue源码
date: 2022-10-02 11:23:51
categories: frontend
tags: [interview]
---

...

<!-- more -->

声明式的开发范式,比命令式的开发范式,可维护性更强

### 开头

packages\runtime-dom\src\index.ts(createApp)
packages\runtime-core\src\apiCreateApp.ts(createApp)

1. virtual dom

- 用 JavaScript 对象模拟真实 DOM 树，对真实 DOM 进行抽象；
- diff 算法 — 比较两棵虚拟 DOM 树的差异；
- pach 算法 — 将两个虚拟 DOM 对象的差异应用到真正的 DOM 树。

2. diff

比较两颗树的时间复杂度
对于旧树上的点 A 来说，它要和新树上的所有点比较，复杂度为 O(n)，然后如果点 A 在新树上没找到的话，点 A 会被删掉，然后遍历新树上的所有点找到一个去填空，复杂度增加为了 O(n^2)，这样的操作会在旧树上的每个点进行，最终复杂度为 O(n^3)。

Vue 进行了优化·O(n3) 复杂度的问题转换成 O(n) 复杂度的问题(只比较同级不考虑跨级问题)，因为在前端操作 dom 的时候了，不会把当前元素作为上一级元素或下一级元素，很少会跨越层级地移动 Dom 元素，常见的都是同级的比较。 所 以 Virtual Dom 只会对同一个层级的元素进行对比。

当数据发生改变时，set 方法会调用 Dep.notify 通知所有订阅者 Watcher(组件)，订阅者就会调用 patch 给真实的 DOM 打补丁，更新相应的视图

diff 算法是根据 beforeUpdate 和 updated 前后改变的虚拟 dom 进行精细化比对找到虚拟 dom 的改变，然后使用 patch 将最小量改变更新到真实的 dom 树上。

快速 Diff 算法:增加预处理，对于相同的前置节点和后置节点，由于它们在新旧两组子节点中的相对位置不变，所以不需要移动节点，但是仍然需要打补丁。 --更新 key 相同的节点

3. reactivity 响应式原理

### 3 相比 2 的提升

mixins 数据来源不清晰

逻辑组织方式(composition-api)+ monorepo + typescript + 双端 diff 换成快速 diff + Object.definedProperty 换成了 proxy

### 参考

(v3 源码阅前)[https://juejin.cn/post/7077369940038123550#heading-23]
