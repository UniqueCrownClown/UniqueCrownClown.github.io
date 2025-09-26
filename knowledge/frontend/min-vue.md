---
title: vue3.0
date: 2020-09-21 11:23:51
categories: frontend #文章分类
tags: [vue3.0] #文章标签，可以一次添加多个标签
---

min-vue...

<!-- more -->

## 本地路径

H:\webspace\clown\vue3\element-plus

H:\webspace\clown\vue3\core

## 项目代码管理方式

Monorepo

## 核心模块

- reactivity



- runtime

runtime-core

runtime-dom


- compiler

compiler-core

- baseParse，对组件 template 进行词法分析，生成对应的抽象语法树 AST。
- transfrom（转化）AST，针对每一个 AST Element，进行不同的 transform 处理，例如 v-on、slot、v-if、纯文本元素等等。
- generate，根据转化后的 AST 来生成对应的可执行函数。


compound 复合,化合物

interpolation 插值 插入文字

## vitest

beforeEach 

vi.fn
