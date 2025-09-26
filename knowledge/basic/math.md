---
title: 简单数学
date: 2022-09-18
categories: 知识笔记 #文章分类
tags: [简单数学] #文章标签，可以一次添加多个标签
---

数学...

<!-- more -->

## 单位

亩是中国市制土地面积单位，一亩等于六十平方丈，大约666.667平方米。十五亩等于一公顷。

香港房屋面积的计算特点主要是使用英制单位平方英尺。这与内地普遍使用的公制单位平方米不同。香港房屋面积通常指的是实际使用面积，不包括公摊面积

1 平方米=10.7639104167 平方英尺



## 数的定义

整数是正整数、零、负整数的集合
自然数是正整数、零

## 高阶函数

高阶函指使用其他函数作为参数、或者返回一个函数作为结果的函数。

柯里化函数、偏函数，都是妥妥的高阶函数！传入一个原函数，返回一个新函数。新函数继承了原函数的能力，又发展出不同的新能力！！

- 柯里化

英语：Currying(果然是满满的英译中的既视感)，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

```js
// 普通的add函数
function add(x, y) {
  return x + y;
}

// Currying后
function curryingAdd(x) {
  return function (y) {
    return x + y;
  };
}

add(1, 2); // 3
curryingAdd(1)(2); // 3
```

- 偏函数

偏函数是指固定一个函数的一个或者多个参数,返回一个新的函数,这个函数用于接受剩余的参数

```js
function add(a, b) {
  return a + b;
}
// 生产偏函数的工厂
function partial(fn, a) {
  return function (b) {
    return fn(a, b);
  };
}
var parAdd = parital(add, 1);
console.log(parAdd(2)); // 3
```

- 缓存函数

```js
function cached(fn) {
  const cacheObj = Object.create(null);

  return function cachedFn(str) {
    if (!cacheObj[str]) {
      let result = fn(str);
      cacheObj[str] = result;
    }
    return cacheObj[str];
  };
}

const calculate = (num) => {
  const startTime = new Date();
  for (let i = 0; i < num; i++) {}
  const endTime = new Date();
  return endTime - startTime;
};
// 经过缓存函数 cashed 将原函数 calculate 封装,让原函数具有缓存的新功能

let cashedCalculate = cached(calculate);

cashedCalculate(10_000_000_000); // 10465
```
