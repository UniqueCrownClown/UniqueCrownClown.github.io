---
title: 设计模式
date: 2017-09-18 11:23:51
categories: js #文章分类
tags: [设计模式] #文章标签，可以一次添加多个标签
---

设计模式...

<!-- more -->

## 设计模式原则

1. 单一职责原则
2. 最少知识原则
3. 开放封闭原则
4. 里式替换原则

子类对象应该能够替换掉父类对象，并且不会影响程序的正确性。具体来说，LSP 要求我们在设计时，应该保证子类对象可以完全替代父类对象，而不需要修改原有代码。这样可以提高代码的可复用性、可扩展性和可维护性。在实际开发中，我们可以通过多态、抽象类、接口等方式来实现 LSP。

5. 接口隔离原则

客户端不应该依赖于它不需要的接口。具体来说，ISP 要求我们将接口细化，将臃肿的接口拆分成更小的、更具体的接口，这样可以避免因为接口过于臃肿而导致的代码耦合和不必要的依赖关系。在实际开发中，我们可以通过接口抽象、接口适配等方式来实现 ISP。

6. 依赖反转原则

高层模块不应该依赖于低层模块，二者都应该依赖于抽象。具体来说，DIP 要求我们在设计系统时，应该针对接口编程，而不是针对实现编程，这样可以使得系统更加灵活、可扩展和易于维护。在实际开发中，我们可以通过依赖注入、工厂模式等方式来实现 DIP

## 设计模式汇总

### 代理模式

```js
function Mx() {
  this.sing = function () {
    console.log("可以开演唱会...");
  };
}

function Jjr() {
  this.sing = function (money) {
    if (money >= 200000000) {
      //通知主人 可以开演唱会啦
      new Mx().sing();
    }
  };
}

var jj = new Jjr();
jj.sing(200000001);
```

### 适配器模式

适配器模式可用来在现有接口和不兼容的类之间进行适配。它被添加到现有代码中来协调两个不同的接口。就类似于苹果电脑的 type-c 接口和 U 盘之间的接口转换器。

```js
function Ipad() {
  this.music = function () {
    console.log("ipad可以播放音乐");
  };
}
//定义一个适配器  判读产品功能
function Adapter(pro) {
  this.pro = pro; //某个商品
  this.music = function () {
    if (this.pro.music) {
      console.log("可以播放音乐");
    } else {
      console.log("不可以播放音乐");
    }
  };
  this.phone = function () {
    if (this.pro.phone) {
      console.log("可以打电话");
    } else {
      console.log("不可以打电话");
    }
  };
}

var ipad = new Ipad();
var adapter = new Adapter(ipad);
adapter.music();
adapter.phone();
```

### 发布订阅模式/观察者模式

发布-订阅者模式又叫观察者模式，它定义了对象间一对多的关系，让多个观察者对象同时监听某一主题对象，当发布者发生改变，所有的订阅者都会得到通知。

可以广泛应用于异步编程中，这是一种替代传递回调函数的方案
可以取代对象之间硬编码的通知机制，一个对象不用再显式地调用另外一个对象的某个接口

优点：

1. 当发布者状态改变，会自动通知所有订阅者。
2. 发布者和订阅者耦合性降低。

缺点：

3. 创建订阅者需要消耗一定的事件和内存
4. 虽然可以弱化对象间的关系，但是过度使用，会使得代码不好维护。

实现思路，定义一个带有标记订阅类型 key 的缓存数组，下方例子的 subs

```js
class EventEmitter {
	constructor() {
		this.subs = Object.create(null)
	}
	 // { 'click': [fn1, fn2], 'change': [fn] }
	$on(eventType, handler) {
		this.subs[eventType] = this.subs[eventType] || []
		this.subs[eventType].push(handler)
	}
	$emit(eventType,args) {
		if (this.subs[eventType].forEach(handler => {
			handler(args)
		}))
	}
  $off(eventType) {
    delete this.subs[eventType]
  }
}

// 测试
   let em = new EventEmitter()
   em.$on('click', () => {
     console.log('click1')
   })
   em.$on('click', () => {
     console.log('click2')
   })
   em.$on('fn', ()=> {
   	console.log('fn')
   })
   em.$emit('click')
   em.$emit('fn')

```

### 建造者模式

将一个复杂的对象分解成多个简单的对象来进行构建，将复杂的构建层与表示层分离，使得相同的构建过程可以创建不同的表示的模式便是建造者模式。

优点

1. 建造者模式的封装性很好，对象本身与构建过程解耦。
2. 建造者模式很容易进行扩展。如果有新的需求，通过实现一个新的建造者类就可以完成。

```js
function gameComputerBuilder() {
  this.buildMainboard = function () {
    console.log("游戏主板");
  };
  this.buildCPU = function () {
    console.log("游戏CPU");
  };
  this.buildHardDisk = function () {
    console.log("游戏硬盘");
  };
  this.getComputer = function () {
    return "游戏电脑";
  };
}

function officeComputerBuilder() {
  this.buildMainboard = function () {
    console.log("办公主板");
  };
  this.buildCPU = function () {
    console.log("办公CPU");
  };
  this.buildHardDisk = function () {
    console.log("办公硬盘");
  };
  this.getComputer = function () {
    return "办公电脑";
  };
}

function Operator() {
  this.startBuild = function (builder) {
    builder.buildMainboard();
    builder.buildCPU();
    builder.buildHardDisk();
    return builder.getComputer();
  };
}

const op = new Operator();
const gameComputer = new gameComputerBuilder();
const officeComputer = new officeComputerBuilder();
const gc = op.startBuild(gameComputer);
console.log(gc);
const oc = op.startBuild(officeComputer);
console.log(oc);
```

### 原型链模式

对象名.属性名的方式获取属性值的时候，首先在对象的私有属性上进行查找
如果私有中存在这个属性，则先用私有属性，如果私有的没有，则通过 \_\_proto\_\_找到所属类的原型
（类的原型上定义的属性和方法都是当前实例公有的属性和方法），
原型上存在的话，获取的是公有的属性值，如果原型上也没有，则继续通过原型链上的 \_\_proto\_\_
继续向上查找，一直找到 object.prototype 为止
这种查找机制就是原型链模式

```js
console.log(Object.prototype.__proto__ === null); //true
```

规则

1. 每一个函数数据类型（普通函数／类）都有一个天生自带的属性：prototype（原型），并且这个属性是一个对象数据类型的值

2. 并且 prototype 上浏览器天生给它加了一个属性 construtor（构造函数），属性值是当前函数（类）本身

3. 每一个对象数据类型(普通的对象、实例、prototype...)天生自带一个属性：\_\_proto\_\_(左右各两个\_),属性值是当前实例所属类的原型（prototype）

4. Object 是 js 中所有对象数据类型的基类（最顶层的类）

5. 在 Object.prototype 上没有\_\_proto\_\_这个属性

[![zEZkND.png](https://s1.ax1x.com/2022/11/15/zEZkND.png)](https://imgse.com/i/zEZkND)

### 单例模式

```js
var Single = (function () {
  var instance;
  function init() {
    //定义私有的方法和属性
    return {
      //定义共有的方法和属性
    };
  }

  return {
    //获取实例
    getInstance: function () {
      if (!instance) {
        instance = init();
      }
      return instance;
    },
  };
})();
var obj1 = Single.getInstance();
var obj2 = Single.getInstance();

console.log(obj1 === obj2); //true
```

### 策略模式优化 if else 循环

```js
 // 有五个对象
obj1 = {
name:“obj1”,
option:{
do:“case1”
}
}
…
obj5 = {
name:“obj5”,
option:{
do:“case5”
}
}
// 然后需要判断每个对象的名称执行不同的操作 doSomething1()…doSomething5()
```

1. if else 实现

```js
function handle(obj){
    if(obj.name == "obj1"){
        doSomething1();
    }else if(obj.name == "obj2"){
        doSomething2();
    }else if(obj.name == "obj3"){
        doSomething3();
    }else if(obj.name == "obj4"){
        doSomething4();
    }else if(obj.name == "obj5"){
        doSomething5();
    }
}
```

2. switch case 实现

```
function handle(obj){
    switch(obj.name) {
        case "obj1":
            doSomething1();
           break;
        case "obj2":
            doSomething2();
           break;
        case "obj3":
            doSomething3();
           break;
        case "obj4":
            doSomething4();
           break;
        case "obj5":
            doSomething5();
           break;
        default:
           return;
   }
}
```

3. 策略树实现

```
var stats ={
    obj1:function(){
        doSomething1();
    },
    obj2:function(){
        doSomething2();
    },
    obj3:function(){
        doSomething3();
    },
    obj4:function(){
        doSomething4();
    },
    obj5:function(){
        doSomething5();
    }
};

// 调用
function handle(obj){
stats[obj.name]();
}

```

4. 多条件判断执行同一操作

旧:

```js
// 多条件判断开始，如下

if (obj.radio1 || obj.checkbox1.length > 0 || obj.input1 ...) {
  // do something
} else {
  // 条件不符，提示
  this.$message({
    message: '请选择条件后重试',
    type: 'warning'
  })
  return false
}

```

新：

```js
// 多条件判断开始，如下

const arr = [
  obj.radio1,
  obj.checkbox1.length,
  obj.input1
  ...
]

const arr1 = arr.map(item => Boolean(item))
if (arr1.includes(true)) {
  // do something
} else {
  // 条件不符，提示
  this.$message({
    message: '请选择条件后重试',
    type: 'warning'
  })
  return false
}

```

### 抽象工厂模式

原理: 将原始方法封装到函数中，并返回这个对象。
优点: 能解决多个相似问题。
缺点: 对象类型不知道，因为 typeof 返回结果都为 object。

```js
function Animal(args) {
  var obj = new Object();
  obj.name = args.name;
  obj.color = args.color;
  obj.getInfo = function () {
    console.log(obj.name + "; " + obj.color);
  };
  return obj;
}
var cat = Animal({ name: "cat", color: "yellow" });
var dog = Animal({ name: "dog", color: "black" });

cat.getInfo();
dog.getInfo();

console.log(typeof cat);
console.log(typeof dog);

//cat; yellow
//dog; black
//object
//object
```

### 构造函数模式

原理: 在构造函数内使用 this 关键字，创建属性和方法。在用 new 关键词创建实例，通过传参实现不同的实例。
优点: 可以识别对象类型
缺点: 构造函数会重复生成函数，为每个对象都创建独立的函数版本

```js
function Car(color, speed, price) {
  this.color = color;
  this.speed = speed;
  this.price = price;
  this.showColor = function () {
    console.log(this.color);
  };
}
var car1 = new Car("yellow", "100", 100000);
var car2 = new Car("red", "150", 1300000);
console.log(car1, car2);
console.log(car1 instanceof Car); //true

//Car {color: "yellow", speed: "100", price: 100000, showColor: ƒ}
//color: "yellow"
//price: 100000
//showColor: ƒ ()
//speed: "100"
//__proto__: Object

//Car {color: "red", speed: "150", price: 1300000, showColor: ƒ}
//color: "red"
//price: 1300000
//showColor: ƒ ()
//speed: "150"
//__proto__: Object
```

### 洋葱模型

```js
const compose = require("koa-compose");
class Onion {
  constructor() {
    this.middleware = [];
  }
  use(fn) {
    if (typeof fn !== "function")
      throw new TypeError("middleware must be a function!");
    this.middleware.push(fn);
    return this;
  }
  start(context) {
    const fnMiddleware = compose(this.middleware);
    const handleRes = () => context;
    return fnMiddleware(context).then(handleRes);
  }
}

let onion = new Onion();

function loggerMiddleware() {
  return function (ctx, next) {
    console.log(`转换前：${ctx.content}`);
    next();
    console.log(`转换后：${ctx.content}`);
  };
}
function titleCase(text) {
  return text.trim().replace(text[0], text[0].toUpperCase());
}

function titleCaseMiddleware() {
  return function (ctx, next) {
    ctx.content = titleCase(ctx.content);
    next();
  };
}
function lowercaseMiddleware() {
  return function (ctx, next) {
    ctx.content = ctx.content.replace(/[A-Z]+/g, function (str) {
      return titleCase(str.toLowerCase());
    });
    next();
  };
}

onion.use(loggerMiddleware());
onion.use(lowercaseMiddleware());
onion.use(titleCaseMiddleware());

var obj = { content: "my name is JIOHO" };
onion.start(obj).then((res) => {
  console.log("================");
  console.log("start 处理结果:", res);
});
```

#### koa-compose 的核心实现

```js
/**
 * Compose `middleware` returning
 * a fully valid middleware comprised
 * of all those which are passed.
 *
 * @param {Array} middleware
 * @return {Function}
 * @api public
 */
function compose(middleware) {
  // 省略部分代码
  return function (context, next) {
    // last called middleware #
    let index = -1;
    return dispatch(0);
    function dispatch(i) {
      if (i <= index)
        return Promise.reject(new Error("next() called multiple times"));
      index = i;
      let fn = middleware[i];
      if (i === middleware.length) fn = next;
      if (!fn) return Promise.resolve();
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err);
      }
    }
  };
}
```

### inversify

    (中文文档)[https://doc.inversify.cloud/zh_cn/]

    ts依赖注入的优秀写法

    https://www.cnblogs.com/dashnowords/p/14380955.html#%E4%BE%9D%E8%B5%96%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81%E6%B3%A8%E5%85%A5
