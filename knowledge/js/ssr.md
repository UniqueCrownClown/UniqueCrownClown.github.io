---
title: ssr
date: 2017-05-21 11:23:51
categories: js #文章分类
tags: [ssr] #文章标签，可以一次添加多个标签
---

ssr...

<!-- more -->

## 服务端渲染

所谓的服务端渲染就是指在后台生成 html 文件传给前端,在用户体验要求比较高的页面（首屏）、重复较多的公共页面可以考虑使用服务器渲染，减少 ajax 请求和提升用户体验。加载首页，不发请求，一开始就请求返回页面

### nuxt.js

Nuxt.js 通过自身的内部封装机制，映射 page 文件夹下的组件到路由文件，自动配置路由文件，这样就相当于将路由方案完全解耦，开发者再也不用操心 router.js 文件，只要专注自己的 page 文件夹以及里面的组件文件就好了，这就是一个比较典型的降低耦合的一个例子。
可以了解参考一下

#### 脚手架

nuxt 官方脚手架(create-nuxt-app)[https://github.com/nuxt/create-nuxt-app]

#### 简单使用

#### Universal 和 Spa 两种 render mode 的区别

spa 是单页，所以只有一个入口文件，sitemap 也就只有一个 url，这会导致网站辛辛苦苦搭建的服务端渲染最多只被搜索引擎收录一个页面。Universal 则能实现所有网站路径完全被收录

sitemap.xml 网站地图，主要作用是告知搜索引擎站点中存在的链接，引导抓取收录

#### 同构

⼀般是指服务端和客户端的同构，意思是服务端和客户端都可以运⾏的同⼀套代码程序，是 SSR 和 SPA ⽅式的⼀个整合。

- 提⾼代码复⽤率；
- 提⾼代码可维护性；
- 服务端渲染,SSR 的优势

#### prisma

Prisma 支持 4 种开发语言，分别是 javascript ( nodejs 后端 )，TypeScript，flow，Go。未来还会支持更多的语言。

```shell
npx prisma generate # 生成模型文件
npx prisma migrate dev --name init # 根据模型生成数据和sql

npx prisma migrate save
npx prisma migrate up
```

#### graphql

restful API 一般需要使用 Swagger 等等单独发布文档站点，而 GraphQL 则是自文档化的。它的文档总是随代码同步更新的。

注入方式

- 模式优先
- 代码优先(\*)

(graphql 的 prisma 解决方案)[https://www.prisma.io/graphql]
(prisma-examples-typegraphql)[https://github.com/prisma/prisma-examples/tree/latest/typescript/graphql-typegraphql]

其实 Resolvers 就相当于 Controllers，只是思想不一样，底层不一样

在 NestJS 中给我们提供了自动生成器，直接在 GraphQLModule.forRoot({}) 配置项中添加 autoSchemaFile 自动生成 Schema 文件到指定路径。

- query

- mutation

- subscription

基于 websocket 制作实时应用程序

#### springboot下的graphQL

[netflix开源的集成DGS组件](https://netflix.github.io/dgs/)

#### reflect-metadata
