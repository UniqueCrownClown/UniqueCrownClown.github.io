---
title: es6笔记
date: 2017-10-21 11:23:51
categories: js #文章分类
tags: [es6] #文章标签，可以一次添加多个标签
---

es6...

<!-- more -->

### javascript 变量

#### es 变量数据类型值简单分类

1. 基本类型值--基本类型值在内存中占据固定大小的空间,因此保存在栈内存(Undefined,Null,Boolean,Number,String)

2. 引用类型值--引用类型值保存在堆内存,变量中保存的实际上是一个指针，这个指针指向内存中的另一个位置，该位置保存对象

因此如果想要给一个变量赋值一个引用类型的值，**那么则必须在堆内存中为这个值分配空间**，由于这种值的大小不固定，因此不能把她保存到栈内存中，但是内存地址的大小是固定的，因此可以将内存地址保存在栈内存中。这样，在查询引用类型的变量时，就可以首先从栈中读取内存地址，在找到在堆内存中保存的值。**这种访问方式叫做引用访问，因为我们不是操作的实际的值，而是被那个值引用的对象。**

#### 复制变量值

- 从一个变量向另一个变量复制基本类型的值，会在栈中创建一个新值，然后把该值复制到为新变量分配的位置上
- 当一个变量向另一个变量复制引用类型的值时，同样也会将存储在栈中的值复制一份放到为新变量分配的空间中。不同的是，这个值的副本实际上是一个指针，而这个指针指向存储在堆中的一个对象。复制操作结束后，两个变量实际上是引用的同一个对象。因此改变其中一个另外一个也会改变。

#### 传递参数

在向参数传递基本类型的值时，被传递的值会被复制给一个局部变量（即命名参数，或者就是 arguments 对象中的一个元素），在向参数传递引用类型的值时，会把这个值在内存中的地址复制给一个局部变量，因此这个局部变量的变化会反映在函数的外部。

### 字符串变量

\${}中可以放入任意的 JavaScript 表达式，可以进行运算，以及引用对象属性。

```js
const name = "小明";
const score = 59;
const result = `${name}${score > 60 ? "的考试成绩及格" : "的考试成绩不及格"}`;
```

### 函数

万物皆对象。方法（Function）是对象，方法的原型(Function.prototype)是对象,Object.prototype 的\_\_proto\_\_属性指向 null。

```js
Function.prototype.__proto__ === Object.prototype;
(Function.__proto__ === Object.prototype) === Function.prototype;
```

#### Function 与 Object 的原型链关系

<!-- ![](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed/hexo/knowledge/nodeJsPic/chain.png) -->

[![XpB63R.png](https://s1.ax1x.com/2022/05/23/XpB63R.png)](https://imgtu.com/i/XpB63R)

```js
//构造函数模式
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.getName = function () {
    return this.name;
  };
}
let p1 = new Person("zhangsan", 12);
let p2 = new Person("wangwu", 23);
console.log(p1.__proto__ == Person.prototype); //true
console.log(Person.__proto__ == Function.prototype); //true
console.log(Function.prototype.__proto__ == Object.prototype); //true
console.log(Function.__proto__ == Object.prototype); //false
console.log(p1 instanceof Person); //true
console.log(p1 instanceof Function); //false p1 ...(.__proto__)到达不了Function.prototype
console.log(p1 instanceof Object); //true
console.log(Function.prototype.__proto__ == Object.prototype); //true
console.log(Object.__proto__ == Function.prototype); //true
```

#### 箭头函数和普通函数

箭头函数不会创建自己的this， 所以它没有自己的this，它只会在自己作用域的上一层继承this。所以箭头函数中this的指向在它在定义时已经确定了，之后不会改变。

所有函数在执行时，会创建一个函数执行上下文，普通函数的执行上下文中会有一个变量 this,而箭头函数没有。
箭头函数中如果出现了 this ，它会永远去拿定义时、作用域链上、最近的那个 this
因为箭头函数没有自己的 this 变量，我们就没有办法修改 this 的指向，所以也不可以将其作为构造函数、它也没有 prototype 对象。
箭头函数没有 arguments 对象
是在所有普通函数中都可用的一个局部变量。 代表函数调用时传递的参数，arguments 对象不是一个数组，是一个类数组。它除了**length 属性和索引**之外，没有任何数组属性，比如 slice 等方法。

所以通常会使用使用
Array.prototype.slice.call(arguments)
[].slice.call(arguments)
[...arguments] 的方式，将它转换成一个数组

#### class 类(构造函数)

1. 通过 class 关键字，可以定义类

```js
//定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return "(" + this.x + ", " + this.y + ")";
  }
}
```

2. 构造函数的 prototype 属性，在 ES6 的“类”上面继续存在。事实上，类的所有方法都定义在类的 prototype 属性上面。

```js
class Point {
  constructor() {
    // ...
  }

  toString() {
    // ...
  }

  toValue() {
    // ...
  }
}

// 等同于

Point.prototype = {
  toString() {},
  toValue() {},
};
```

3. 类的内部所有定义的方法，都是不可枚举的（non-enumerable）。

- JavaScript 对象的属性可分为可枚举和不可枚举，它是由属性的 enumeration 值决定的，true 为可枚举，false 为不可枚举
- js 中原型属性一般是不可枚举的，而自己定义的属性一般是可枚举的
- 可以通过 propertyIsEnumerable()方法判断该属性是否可枚举
- 属性的枚举性影响的函数
  - for...in //遍历原型与实例上的**所有可枚举属性**
  - Object.keys(); //只能返回对象本身具有的可枚举属性。
  - JSON.stringify(); //只能读取对象本身的可枚举属性，并序列化为 JSON 对象。
  - Object.getOwnPropertyNames() //遍历自身所有属性（不论是否是可枚举的）,不包括原型链上面的.
  - Object.assign() es6 新增 //自身的可枚举属性。

```js
var person = {
  name: "zhou",
  age: "12",
  sex: "girl",
};
console.log(person.propertyIsEnumerable("sex"));
Object.defineProperty(person, "age", {
  enumerable: true, //可以被枚举
});
Object.defineProperty(person, "sex", {
  enumerable: false, //不可以被枚举
});

for (var k in person) {
  console.log(person[k]); //a,可以被枚举
}
//12
//zhou
```

4. Class 不存在变量提升

5. 类的属性名，可以采用表达式。

```js
let methodName = "getArea";
class Square {
  constructor(length) {
    // ...
  }

  [methodName]() {
    // ...
  }
}
```

6. constructor 方法是类的默认方法，通过 new 命令生成对象实例时，自动调用该方法。一个类必须有 constructor 方法，如果没有显式定义，一个空的 constructor 方法会被默认添加。constructor 方法默认返回实例对象（即 this），完全可以指定返回另外一个对象。

7. 可以通过实例的 \_\_proto\_\_属性为 Class 添加方法,使用实例的 \_\_proto\_\_属性改写原型，必须相当谨慎，不推荐使用，因为这会改变 Class 的原始定义，影响到所有实例。

8. 多态实现的一个例子

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}
class Dog extends Animal {
  constructor(name) {
    super(name); // 调用超类构造函数并传入name参数
  }

  speak() {
    console.log(`${this.name} barks.`);
  }
}

var d = new Dog("Mitzie");
d.speak(); // 'Mitzie barks'
```

```js
var p1 = new Point(2, 3);
var p2 = new Point(3, 2);

p1.\_\_proto\_\_.printName = function () {
  return "Oops";
};

p1.printName(); // "Oops"
p2.printName(); // "Oops"

var p3 = new Point(4, 2);
p3.printName(); // "Oops"
```

9. 原型链继承和构造函数继承

- 构造函数继承的问题：构造函数外在原型上定义方法，不能重用，原型链继承可以
- 原型链继承的问题：原型中包含的引用值会在所有实例间共享。构造函数继承可以
- class 继承没有上面两个问题

```js
function SuperType() {}
SuperType.prototype.sayName = () => {
  console.log("bob");
};

function SubType() {
  SuperType.call(this); // 构造函数继承
}

let p1 = new SubType();
console.log(p1.sayName()); // Uncaught TypeError: p1.sayName is not a functio

function SuperType() {}
SuperType.prototype.sayName = () => {
  console.log("bob");
};

function SubType() {}
SubType.prototype = new SuperType(); // 原型链继承

let p1 = new SubType();
console.log(p1.sayName()); // bob

function SuperType() {
  this.name = ["bob", "tom"];
}

function SubType() {}
SubType.prototype = new SuperType(); // 原型链继承

let p1 = new SubType();
p1.name.push("jerry");

let p2 = new SubType();
console.log(p2.name); //  ['bob', 'tom', 'jerry']

function SuperType() {
  this.name = ["bob", "tom"];
}

function SubType() {
  SuperType.call(this); // 构造函数继承
}

let p1 = new SubType();
p1.name.push("jerry");

let p2 = new SubType();
console.log(p2.name); //  ['bob', 'tom']
```

### 扩展运算符

1. 不定参数长度

- es5 实现不定参数

```js
//在以往，我们给函数传不确定参数数量时，是通过arguments来获取的
function sum() {
  console.log(arguments); // { '0': 1, '1': 2, '2': 3, '3': 4, '4': 5 }
  let total = 0;
  let { length } = arguments;
  for (let i = 0; i < length; i++) {
    total += arguments[i];
  }
  return total;
}

console.log(sum(1, 2, 3, 4, 5)); // 21
```

- es6 实现

```js
// 接下来我们用扩展运算符看看
function sum(...args) {
  // 使用...扩展运算符
  console.log(args); // [ 1, 2, 3, 4, 5, 6 ] args是一个数组
  return eval(args.join("+"));
}

console.log(sum(1, 2, 3, 4, 5, 6)); // 21
```

2. 连接数组和对象

```js
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
let arr3 = [...arr1, ...arr2];

// 使用扩展运算符
let name = { name: "邵威儒" };
let age = { age: 28 };
let person = { ...name, ...age };
```

3. 复制和合并对象,对象重复以后面的为主，相当于重写(属于修改原对象类型的 function)

```js
var obj = { a: 1 };
var obj2 = { b: 2 };
var copy = Object.assign({}, obj);
var copy2 = Object.assign(obj, obj2);
console.log(copy); // { a: 1 }
console.log(copy2); //  { a: 1 ,b: 2 }
```

### prototype

#### prototype 说明

prototype 是函数的一个属性，并且是函数的原型对象。引用它的必然是函数
而\_\_proto\_\_是对象具有的一个属性，可称为隐式原型，一个对象的隐式原型指向构造该对象的构造函数的原型，这也保证了实例能够访问在构造函数原型中定义的属性和方法。
Array.prototype 是一个数组
String.prototype 是一个字符串
Object.prototype 是一个对象

#### prototype 的应用

- 原形对象增加公有属性
- 原形对象增加公有函数
- 实现原形继承

```js
// slice() 方法返回一个新的数组对象，
// 这一对象是一个由 begin 和 end（不包括 end）决定的原数组的浅拷贝。原始数组不会被改变
// 经典例子(从第二个实参数开始获取func函数的实参)
function func() {
  var args = Array.prototype.slice.call(arguments, 1);
  return args;
}
alert(func(0, 1, 2, 3, 4)); //给func函数传实参 [1, 2, 3, 4]

console.log([1, 2, 3, 4, 5].slice(-1)); // 5
console.log([1, 2, 3, 4, 5].slice(-2)); // [4,5]
console.log([1, 2, 3, 4, 5].slice(1)); // [2,3,4,5]
console.log([1, 2, 3, 4, 5].slice(2)); // [3,4,5]
```

#### \_\_proto\_\_ 和 prototype

\_\_proto\_\_和 prototype 的区别

1. \_\_proto\_\_（隐式原型）与 prototype（显式原型）prototype 和\_\_proto\_\_之间的唯一区别就在于前者是构造函数的属性，而后者是对象的属性。
2. \_\_proto\_\_是任何对象的属性，指向该对象的构造函数的原型对象，而 prototype 是构造函数的属性指向属于该构造函数的原型对象
3. \_\_proto\_\_指向的是当前对象的原型对象，而 prototype 指向的，是以当前函数作为构造函数构造出来的对象的原型对象。
4. 关于 prototype 与\_\_proto\_\_容易让人疑惑的点是，构造函数也有\_\_proto\_\_属性。之所以会这样，是因为构造函数也是一个对象，它是构造函数 Function()的实例对象。因此，所有构造函数的\_\_proto\_\_属性都指向 Function 构造函数的原型对象。同时，原型对象是特殊的对象，每个构造函数的原型对象都会有一个\_\_proto\_\_属性，共同指向构造函数 Object()的原型对象。构造函数 Object()的原型对象，作为除自身以外被所有原型对象指向的对象，其担当着根节点的角色，它的\_\_proto\_\_属性指向 null。
5. 隐式原型指向创建这个对象的函数(constructor)的 prototype
   图例分析
   <!-- ![](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed/hexo/knowledge/nodeJsPic/proto.jpg) -->

   [![XpBcg1.jpg](https://s1.ax1x.com/2022/05/23/XpBcg1.jpg)](https://imgtu.com/i/XpBcg1)

6. 构造函数 Foo()构造函数的原型属性 Foo.prototype 指向了原型对象，在原型对象里有共有的方法，所有构造函数声明的实例（这里是 f1，f2）都可以共享这个方法。
7. 原型对象 Foo.prototypeFoo.prototype 保存着实例共享的方法，有一个指针 constructor 指回构造函数。
8. 实例 f1 和 f2 是 Foo 这个对象的两个实例，这两个对象也有属性\_\_proto\_\_，指向构造函数的原型对象，这样子就可以像上面 1 所说的访问原型对象的所有方法啦。另外：构造函数 Foo()除了是方法，也是对象啊，它也有\_\_proto\_\_属性，指向谁呢？指向它的构造函数的原型对象呗。函数的构造函数不就是 Function 嘛，因此这里的\_\_proto\_\_指向了 Function.prototype。其实除了 Foo()，Function(), Object()也是一样的道理。原型对象也是对象啊，它的\_\_proto\_\_属性，又指向谁呢？同理，指向它的构造函数的原型对象呗。这里是 Object.prototype.最后，Object.prototype 的\_\_proto\_\_属性指向 null。

总结：

1. 对象有属性\_\_proto\_\_,指向该对象的构造函数的原型对象。
2. 方法除了有属性\_\_proto\_\_,还有属性 prototype，prototype 指向该方法的原型对象。
3. 箭头函数不是构造函数,没有 prototype

```js
const Arrow = (fruit) => (fruit = "apple"); // 箭头函数没有prototype
Function Foo(){this.fruit = "apple"}
const f1 = new Foo();
f1.__proto__ === Foo.prototype； // true
Foo.prototype.constructor === Foo // true,构造函数指针又指回他本身
Foo.__proto__ === Function.prototype; // true
Foo.prototype.__proto__ === Object.prototype; // true
String.prototype.__proto__ === Object.prototype; // true
Function.prototype.__proto__ === Object.prototype; // true
Function.prototype === Function.__proto__ // ture,,Function的特别性质
Object.__proto__ === Function.prototype // true
String.prototype === String.__proto__ // false
console.log(Object.prototype.__proto__); //输出null
```

#### 原型链

每个对象都有原型，当访问这个变量的时候，这个变量不存在就访问他的原型，就这样一直循环下去就产生了原型链！
\_\_proto\_\_.\_\_proto\_\_.\_\_proto\_\_——>null (Object)

#### Function 分类

Function 分为两种,,普通函数和构造函数
function xxx 语法可以看成 new Function 的等价形式。
用户自定义的函数通常既可以作为普通函数使用，又可以作为构造函数来制造对象。ES6 新增的 class 语法定义的函数只能作为构造函数，ES6 新增的=>语法定义的箭头函数只能作为普通函数。

#### bind,apply,call

bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

```js
const module = {
  x: 42,
  getX: function () {
    return this.x;
  },
};

const unboundGetX = module.getX;
console.log(unboundGetX()); // The function gets invoked at the global scope
// expected output: undefined

const boundGetX = unboundGetX.bind(module);
console.log(boundGetX());
// expected output: 42
```

偏函数

```js
function list() {
  return Array.prototype.slice.call(arguments);
}

function addArguments(arg1, arg2) {
  return arg1 + arg2;
}

var list1 = list(1, 2, 3); // [1, 2, 3]

var result1 = addArguments(1, 2); // 3

// 创建一个函数，它拥有预设参数列表。
var leadingThirtysevenList = list.bind(null, 37);

// 创建一个函数，它拥有预设的第一个参数
var addThirtySeven = addArguments.bind(null, 37);

var list2 = leadingThirtysevenList();
// [37]

var list3 = leadingThirtysevenList(1, 2, 3);
// [37, 1, 2, 3]

var result2 = addThirtySeven(5);
// 37 + 5 = 42

var result3 = addThirtySeven(5, 10);
// 37 + 5 = 42 ，第二个参数被忽略
```

apply() 方法调用一个具有给定 this 值的函数，以及以一个数组（或一个类数组对象）的形式提供的参数。

```js
const numbers = [5, 6, 2, 3, 7];
const max = Math.max.apply(null, numbers);
console.log(max);
// expected output: 7
const min = Math.min.apply(null, numbers);
console.log(min);
// expected output: 2
```

call()
该方法的语法和作用与 apply() 方法类似，只有一个区别，就是 call() 方法接受的是一个参数列表，而 apply() 方法接受的是一个包含多个参数的数组。
call()允许为不同的对象分配和调用属于一个对象的函数/方法。
call()提供新的 this 值给当前调用的函数/方法。
你可以使用 call 来实现继承：写一个方法，然后让另外一个新的对象来继承它（而不是在新对象中再写一次这个方法）。
使用 call 方法防止调用对象的原型方法被改变

```js
function Product(name, price) {
  this.name = name;
  this.price = price;
}

function Food(name, price) {
  Product.call(this, name, price);
  this.category = "food";
}

console.log(new Food("cheese", 5).name);
// expected output: "cheese"
```

### Set 和 WeakSet,Map 和 WeakMap

1. set 存储值的不重复特性
   对于原始数据类型（boolean，number，string，null，undefined）如果存储相同值则只会保存一个，对于引用类型做“==”判断即引用地址完全相同则只会存一个
   鉴于 set 存储值的不重复特性，经常被用来求数组去重，交集，并集，差集等操作

```js
//set求并集
let arryA&#61; [2,3,4,5,6],arryB &#61; [3,4,5,6,7,8];
let setAB &#61; new Set([...arryA,...arryB]);
let newArryAB &#61; [...setAB];
console.log(newArryAB); //[2,3,4,5,6,7,8]

//求交集
let arryC&#61; [2,3,4,5,6],arryD &#61; [3,4,5,6,7,8];
let setC &#61; new Set(arryC);
let setD &#61; new Set(arryD);
let newArryC_D &#61; arryA.filter(x&#61;&gt;setD.has(x));
console.log(newArryC_D); //[3,4,5,6]

//求差集
let newArryD_C &#61; arryA.filter(x&#61;&gt;!setD.has(x));
let newArryD_D &#61; arryB.filter(x&#61;&gt;!setC.has(x));
let newArryCD &#61; [...newArryD_C,...newArryD_D];
console.log(newArryCD); //[2,7,8]
```

2. Map

- Map 对象保存键值对。任何值(对象或者原始值) 都可以作为一个键或一个值。
- Map 中的键值是有序的
- Object 都有自己的原型，原型链上的键名有可能和你自己在对象上的设置的键名产生冲突，而 map 健不可重复，如果键名冲突则会覆盖对应的值。

3. WeakSet

- WeakSet 和 Set 结构类似，也是不重复的值的集合，但 WeakSet 的成员只能是对象。
- WeakSet 的 API：add() //增 ；delete() //删； has() //是否存在
- 注意：ws 没有 size 属性，不可遍历。因为 WeakSet 的成员都是弱引用，随时可能消失，成员是不稳定的。
- 用途：使用 ws 储存 DOM 节点，就不用担心节点从文档移除时，会引发内存泄漏

4.

- WeakMap 对象是一组键值对的集合，其中的键是弱引用对象，而值可以是任意。
- WeakMap 弱引用的只是键名，而不是键值。键值依然是正常引用。
- WeakMap 中，每个键对自己所引用对象的引用都是弱引用，在没有其他引用和该键引用同一对象，这个对象将会被垃圾回收（相应的 key 则变成无效的），所以，WeakMap 的 key 是不可枚举的。

### javascript 数组常见原型方法

JavaScript 中的数组是一个相当泛用性的数据结构，能当数组，元组，队列，栈进行操作，更好的是 JavaScript 提供了很多原生的高阶函数，便于我们对数组整体操作。

#### Array 构建

```js
const a = new Array(10).filled(null).map((_, index) => index);
const b = [...Array(10)].map((_, index) => index);
```

#### Array.prototype.slice

slice() 方法返回一个新的数组对象，这一对象是一个由 begin 和 end 决定的原数组的浅拷贝（包括 begin，不包括 end）。原始数组不会被改变
**记住记住,slice 经常用到**

```js
// arguments转数组
const args = [].slice.call(arguments);
```

#### Array.at()

```js
const array1 = [5, 12, 8, 130, 44];
let index = 2;
console.log(
  `Using an index of ${index} the item returned is ${array1.at(index)}`
);
// expected output: "Using an index of 2 the item returned is 8"
index = -2;
console.log(`Using an index of ${index} item returned is ${array1.at(index)}`);
// expected output: "Using an index of -2 item returned is 130"
```

#### Array.indexOf&Array.includes

Array.indexOf 返回的是位置的 index,Array.includes 返回 boolean

```js
const characters = [0, 1, 2, 3];

console.log(characters.indexOf(0));
// 0
console.log(characters.includes(0));
// true
```

#### Array.from(new Set(new Array()))

数组去重并转为可做遍历操作的数组对象

#### Array.forEach()

1. 包含异步操作时，await 和 async 无效，可改用 for(item of items){}实现
2. 无法用 break 或者 return 中断循环
3. 解决 tips:

- some()：当内部 return true 时跳出整个循环
- every()：当内部 return false 时跳出整个循环

```js
const a = ["a", "b", "c"];

a.forEach(function (element) {
  console.log(element);
});
```

#### Array.some()&Array.filter()&Array.find()

数组是否含有某元素，，数组含有该元素的唯一记录，，数组含有该元素的记录

```js
const character = [
  { id: 1, name: "ironman", env: "marvel" },
  { id: 2, name: "black_widow", env: "marvel" },
  { id: 3, name: "wonder_woman", env: "dc_comics" },
];

function hasCharacterFrom(env) {
  return (character) => character.env === env;
}

console.log(character.some(hasCharacterFrom("marvel")));
// true

console.log(character.find(hasCharacterFrom("marvel")));
// { id: 1, name: 'ironman', env: 'marvel' } (找到一个就停)

console.log(character.filter(hasCharacterFrom("marvel")));
// [{ id: 1, name: 'ironman', env: 'marvel' },{ id: 2, name: 'black_widow', env: 'marvel' }]
```

#### Array.map()

- map():返回一个新的 Array，每个元素为调用 func 的结果。新数组的长度和原来的是一样的，他只不过是逐一对原来数据里的每个元素进行操作。
- filter():返回一个符合 func 条件的元素数组。筛选条件，把数组符合条件的放在新的数组里面返回。新数组和原来的数组长度不一定一样。
- some():返回一个 boolean，判断是否有元素是否符合 func 条件。数组里面所有的元素有一个符合条件就返回 true。
- every():返回一个 boolean，判断每个元素是否符合 func 条件。数组里面所有的元素都符合才返回 true。
- forEach():没有返回值，只是针对每个元素调用 func 。循环数组。和 for 的用法一样的。

```js
newArray = Array.map((value) => {
  return value + 1;
});
```

#### Array.flat()

> var newArray = arr.flat([depth]);
> 指定巢狀陣列展開的深度。預設為 1

```js
var arr1 = [1, 2, [3, 4]];
arr1.flat();
// [1, 2, 3, 4]

var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();
// [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]
```

#### Array.reduce()

reduce() 方法接收一个函数作为累加器（accumulator），数组中的每个值（从左到右）开始合并，最终为一个值

- 语法

array.reduce(function(total, currentValue, currentIndex, arr), initialValue)

如果没有提供 initialValue，reduce 会从索引 1 的地方开始执行 callback 方法，跳过第一个索引。如果提供 initialValue，从索引 0 开始。

- 用法

1. 计算数组中每个元素出现的次数

```js
var sum = arr.reduce((x, y) => x + y);
var mul = arr.reduce((x, y) => x * y);
```

2. 数组去重

```js
let arr = [1, 2, 3, 4, 4, 1];
let newArr = arr.reduce((pre, cur) => {
  if (!pre.includes(cur)) {
    return pre.concat(cur);
  } else {
    return pre;
  }
}, []);
console.log(newArr); // [1, 2, 3, 4]
```

3. 将二维数组转化为一维

```js
let arr = [
  [0, 1],
  [2, 3],
  [4, 5],
];
let newArr = arr.reduce((pre, cur) => {
  return pre.concat(cur);
}, []);
console.log(newArr); // [0, 1, 2, 3, 4, 5]
```

4. 将多维数组转化为一维

```js
let arr = [
  [0, 1],
  [2, 3],
  [4, [5, 6, 7]],
];
const newArr = function (arr) {
  return arr.reduce(
    (pre, cur) => pre.concat(Array.isArray(cur) ? newArr(cur) : cur),
    []
  );
};
console.log(newArr(arr)); //[0, 1, 2, 3, 4, 5, 6, 7]
```

5. 对象属性求和

```js
var result = [
  {
    subject: "math",
    score: 10,
  },
  {
    subject: "chinese",
    score: 20,
  },
  {
    subject: "english",
    score: 30,
  },
];

var sum = result.reduce(function (prev, cur) {
  return cur.score + prev;
}, 0);
console.log(sum); //60
```

参数：

- callback(执行数组中每个值的函数，包含四个参数)
- previousValue
  上一次调用回调返回的值，或者是提供的初始值（initialValue）
- currentValue
  数组中当前被处理的元素
- index
  当前元素在数组中的索引
- array
  调用 reduce 的数组
- initialValue
  作为第一次调用 callback 的第一个参数。

```js
var total = [0, 1, 2, 3, 4].reduce(function (
  previousValue,
  currentValue,
  index,
  array
) {
  return previousValue + currentValue;
},
10);

console.log(tatal);

//返回初始值10加上数组每项值的累积,tatal:20
```

```js
var flattened = [
  [0, 1],
  [2, 3],
  [4, 5],
].reduce(function (a, b) {
  return a.concat(b);
});

console.log([0, 1].concat([2, 3]));

console.log(flattened);

// flattened is [0, 1, 2, 3, 4, 5]
```

当然最好还是去 mdn 看看具体用法组好

[MDN web docs](https://developer.mozilla.org/it/docs/Web/JavaScript/Reference/Global_Objects/Array)

#### 循环 tips

因为 js 中对数据的遍历除了 for 循环还有 forEach、map、filter、some 等，除了 for 循环外(for,for...of)，其他的遍历都是对键值的遍历，也就是除了存在的元素外的 undefined 不会对不存在的值执行回调.，所以也就不会造成性能损耗

```js

```

### js 同步异步

#### promise

new Promise(executor)

```js
// executor 的函数签名为
function(resolutionFunc, rejectionFunc){
      // 通常是一些异步操作
}
```

Promise.then 方法返回一个新的 promise 实例，这是实现链式调用的根本

1. 基础用用

promise 状态:

- pending 初始状态，既没有被兑现，也没有被拒绝
- fulfilled 意味着操作成功完成
- rejected 意味着操作失败

```js
new promise((resolve, reject) => {
  if (ss) {
    resolve("apple");
  } else {
    reject("peach");
  }
})
  .then((result) => {
    console.log(result); // apple
  })
  .catch((reason) => {
    console.log(reason); //peach
  });
```

.then() 方法需要两个参数，第一个参数作为处理已兑现状态的回调函数，而第二个参数则作为处理已拒绝状态的回调函数。每一个 .then() 方法还会返回一个新生成的 promise 对象，这个对象可被用作链式调用

.catch() 其实只是没有给处理已兑现状态的回调函数预留参数位置的 .then() 而已

```js
myPromise
  .then(handleResolvedA)
  .then(handleResolvedB)
  .then(handleResolvedC)
  .catch(handleRejectedAny);
```

2. 静态方法

- Promise.all(iterable) 等到所有的 promise 对象都成功或有任意一个 promise 失败
- Promise.allSettled(iterable) 返回一个 promise，该 promise 在所有 promise 都敲定后完成，并兑现一个对象数组，其中的对象对应每个 promise 的结果
- Promise.any(iterable) 接收一个 promise 对象的集合，当其中的任意一个 promise 成功，就返回那个成功的 promise 的值。
- Promise.race(iterable) 当 iterable 参数里的任意一个子 promise 成功或失败后，父 promise 马上也会用子 promise 的成功返回值或失败详情作为参数调用父 promise 绑定的相应处理函数，并返回该 promise 对象。
- Promise.all() 方法是 && 的关系；Promise.any() 方法是 || 的关系； Promise.race()方法是 赛跑机制

#### await 和 async

在 forEach 中存在异步操作时用 await 和 async 时有问题，改用 for(item of items)实现

#### generator&yield

function\* 这种声明方式(function 关键字后跟一个星号）会定义一个生成器函数 (generator function)，它返回一个 Generator 对象。
es6 关于 yield 和 next 的用法

1. 接收参数

   ```js
   function* idMaker() {
     var index = arguments[0] || 0;
     while (true) yield index++;
   }

   var gen = idMaker(5);
   console.log(gen.next().value); // 5
   console.log(gen.next().value); // 6
   ```

2. yield\* 示例

   ```js
   function* anotherGenerator(i) {
     yield i + 1;
     yield i + 2;
     yield i + 3;
   }

   function* generator(i) {
     yield i;
     yield* anotherGenerator(i); // 移交执行权
     yield i + 10;
   }

   var gen = generator(10);

   console.log(gen.next().value); // 10
   console.log(gen.next().value); // 11
   console.log(gen.next().value); // 12
   console.log(gen.next().value); // 13
   console.log(gen.next().value); // 20
   ```

3. generator 函数中有 return  
   当在生成器函数中显式 return 时，会导致生成器立即变为完成状态，即调用 next() 方法返回的对象的 done 为 true。
   如果 return 后面跟了一个值，那么这个值会作为当前调用 next() 方法返回的 value 值。再调用 next()就不执行了

4. 使用迭代器遍历二维数组并赋给一维数组

   ```js
   function* iterArr(arr) {
     //迭代器返回一个迭代器对象
     if (Array.isArray(arr)) {
       // 内节点
       for (let i = 0; i < arr.length; i++) {
         yield* iterArr(arr[i]); // (*)递归
       }
     } else {
       // 离开
       yield arr;
     }
   }
   // 使用 for-of 遍历:
   var arr = ["a", ["b", "c"], ["d", "e"]];
   for (var x of iterArr(arr)) {
     console.log(x); // a  b  c  d  e
   }
   
   // 或者直接将迭代器展开:
   var arr = ["a", ["b", ["c", ["d", "e"]]]];
   var gen = iterArr(arr);
   arr = [...gen];
   ```

#### generator && async 和 await

ES2017 标准引入了 async 函数,在异步处理上，async 函数就是 Generator 函数的语法糖。

顺序执行和异步执行

```js
//顺序执行
(async () => {
  const getList = await getList();
  const getAnotherList = await getAnotherList();
})();
//异步执行(方法1)
(async () => {
  const listPromise = getList();
  const anotherListPromise = getAnotherList();
  await listPromise;
  await anotherListPromise;
})();
//异步执行(方法2)
(async () => {
  Promise.all([getList(), getAnotherList()]).then(...);
})();

```

例子：给定一个 URL 数组，如何实现接口的继发和并发？

```js
// 继发一
async function loadData() {
  var res1 = await fetch(url1);
  var res2 = await fetch(url2);
  var res3 = await fetch(url3);
  return "whew all done";
}
// 继发二
async function loadData(urls) {
  for (const url of urls) {
    const response = await fetch(url);
    console.log(await response.text());
  }
}

// 并发一
async function loadData() {
  var res = await Promise.all([fetch(url1), fetch(url2), fetch(url3)]);
  return "whew all done";
}
// 并发二
async function loadData(urls) {
  // 并发读取 url
  const textPromises = urls.map(async (url) => {
    const response = await fetch(url);
    return response.text();
  });

  // 按次序输出
  for (const textPromise of textPromises) {
    console.log(await textPromise);
  }
}
```

#### async 错误捕获

```js
//写法1
async function asyncTask(cb) {
  try {
    const user = await UserModel.findById(1);
    if (!user) return cb("No user found");
  } catch (e) {
    return cb("Unexpected error occurred");
  }
}
//写法2
async function asyncTask(cb) {
  const [err, user] = await UserModel.findById(1);
  if (!user) throw new CustomerError("No user found");
}

// to.js
export default function to(promise) {
  return promise
    .then((data) => {
      return [null, data];
    })
    .catch((err) => [err]);
}
```

### Symbol

本质上是一种唯一标识符，可用作对象的唯一属性名
使用 symbol 在对象上创建私有属性
设置作为对象的 key 的时候，遍历也不可见，只有 Reflect.ownKeys()时才能看到，
配合 json
做对象注入的时候不可见

```js
const person = {
  age: 18,
  [Symbol("name")]: "song",
};
console.log(Object.getOwnPropertyNames(person)); // [ 'age' ] 老写法
console.log(Object.getOwnPropertySymbols(person)); // [ Symbol(name) ] 老写法
console.log(Reflect.ownKeys(person)); // [ 'age', Symbol(name) ]
```

#### Symbol 应用场景

- 对象有些 key-value 不想被枚举出来

可以用 Symbol 来当做对象的属性名,作为属性的属性不会被枚举出来，这也是 JSON.stringfy(obj)时，Symbol 属性会被排除在外的原因

```js
// 遍历不出来
console.log(Object.keys(obj)); // [ 'name', 'age' ]
for (const key in obj) {
  console.log(key); // name age
}
console.log(JSON.stringify(obj)); // {"name":"Sunshine_Lin","age":23}

// 遍历出来
// 方法一
console.log(Object.getOwnPropertySymbols(obj)); // [ Symbol(gender) ]
// 方法二
console.log(Reflect.ownKeys(obj)); // [ 'name', 'age', Symbol(gender) ]
```

- 使用 Symbol 定义类的私有属性

以下例子，PASSWORD 属性无法在实例里获取到

```js
class Login {
  constructor(username, password) {
    const PASSWORD = Symbol();
    this.username = username;
    this[PASSWORD] = password;
  }
  checkPassword(pwd) {
    return this[PASSWORD] === pwd;
  }
}

const login = new Login("123456", "hahah");

console.log(login.PASSWORD); // 报错
console.log(login[PASSWORD]); // 报错
console.log(login[PASSWORD]); // 报错
```

#### symbol.Iterator

Symbol.iterator 为每一个对象定义了默认的迭代器。该迭代器可以被 for...of 循环使用。

内置类型拥有默认的迭代器行为

Array,Set,Map,TypeArray,String,arguments

for...of 语句在可迭代对象（包括 **Array**，**Map**，**Set**，**String**，**TypedArray**，**arguments** 对象等等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句

代替 for...of 手动控制迭代

```js
const arr = ["a", "b", "c", "d", "e"];
const arrIter = arr[Symbol.iterator]();
console.log(arrIter.next().value); // a
console.log(arrIter.next().value); // b
console.log(arrIter.next().value); // c
console.log(arrIter.next().value); // d
console.log(arrIter.next().value); // e
```

```js
function logIterable(it) {
  if (!(Symbol.iterator in it)) {
    console.log(it, " is not an iterable object.");
    return;
  }

  for (const letter of it) {
    console.log(letter);
  }
}

// Array
logIterable(["a", "b", "c"]);
// a
// b
// c

// string
logIterable("abc");
// a
// b
// c

logIterable(123);
// 123 is not an iterable object.
```

### Reflect 与 Proxy 与 Object

#### Proxy 区别于 Object.definedProperty

- Object.defineProperty 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。
- Object.defineProperty **只能监听到属性的读写**，而 Proxy 除读写外还可以监听属性的删除，方法的调用等。

```js
const list = [1, 2, 3];
const listproxy = new Proxy(list, {
  set(target, property, value) {
    target[property] = value;
    return true; // 标识设置成功
  },
});

list.push(4);
```

#### 数组的 Object.definedProperty

#### reflect 说明

Reflect 是一个内置的对象，它提供拦截 JavaScript 操作的方法。这些方法与 proxy handlers 的方法相同
判断一个 obj 有定义或者继承了属性 name: name in obj = Reflect.has(obj,name)
删除一个属性 ：delete obj[name] = Reflect.deleteProperty(obj,name)

- Reflect 的所有属性和方法都是静态的（就像 Math 对象）
- Reflect 是 ES6 为了操作对象而新增的 API,其中的一些方法与 Object 相同, 尽管二者之间存在 某些细微上的差别
- 代理实现,在目标对象之前架设一层拦截，外部所有的访问都必须先通过这层拦截，因此提供了一种机制，可以对外部的访问进行过滤和修改。

```js
let proxy = new Proxy(target, handler); // target是表示所要拦截的对象，handle是用来定制拦截行为的对象

let handler = {
  get: (obj, prop) => 42,
};
// 与从对象 (target[propertyKey]) 中读取属性类似，但它是通过一个函数执行来操作的。
handler.get(target, propertyKey[, receiver]);
// 以函数的方式在对象上设置属性,如果遇到setter,receiver则为setter调用时的this值
handler.set(target, propertyKey, value[, receiver]);
// 静态方法 Reflect.has() 作用与 in 操作符 相同。
handler.has(target, propertyKey);
//
handler.apply(target, thisArgument, argumentsList);
// 允许你使用可变的参数来调用构造函数
handler.construct(target, argumentsList[, newTarget]);
// 返回一个由目标对象自身的属性键组成的数组
handler.ownKeys(target);
// 允许你删除一个对象上的属性。返回一个 Boolean 值表示该属性是否被成功删除。
handler.deleteProperty(target, propertyKey);
// 允许精确添加或修改对象上的属性,包括属性描述符
// attributes里面的如果配置了set()或者get()，那么target对象的propertyKey属性，变成存取器属性
// 如果一个属性是存取器，则读取该属性时，会运行get方法，将get方法得到的返回值作为属性值
// 如果给该属性赋值，则会运行set方法
handler.defineProperty(target, propertyKey, attributes);
// 判断一个对象是否可扩展 （即是否能够添加新的属性）
handler.isExtensible(target);
// Reflect.preventExtensions 方法阻止新属性添加到对象 (例如：防止将来对对象的扩展被添加到对象中。
handler.preventExtensions(target);
// Reflect.getPrototypeOf 返回指定对象的原型 (即内部的 [[Prototype]] 属性的值) 。
handler.getPrototypeOf(target);
// 它可设置对象的原型（即内部的 [[Prototype]] 属性）为另一个对象或 null，
// 如果操作成功返回 true，否则返回 false。
handler.setPrototypeOf(target, prototype);
// 如果在对象中存在，则返回给定的属性的属性描述符。否则返回 undefined。
// propertyKey对应的value是个完整对象,,数据结构为
// {configurable: true, enumerable: true, value: xxxx, writable: true}
// configurable 该属性描述属性是否可以修改
// enumerable 该属性是否可以被枚举
// value 属性值
// writable 该属性是否可以被重新赋值
handler.getOwnPropertyDescriptor(target, propertyKey);

```

#### Object.defineProperty

能劫持变量,实现数据双向绑定

```js
// 如何让(a===1&&a===2&&a===3)的值为true
var value = 1;
Object.defineProperty(window, "a", {
  get() {
    return this.value++;
  },
});

if (a === 1 && a === 2 && a === 3) {
  console.log("Hi Eno!");
}
```

#### 实现观察者模式

```js
// 定义 Set 集合
const queuedObservers = new Set();
// 把观察者函数都放入 Set 集合中
const observe = (fn) => queuedObservers.add(fn);
// observable 返回原始对象的代理，拦截赋值操作
const observable = (obj) => new Proxy(obj, { set });
function set(target, key, value, receiver) {
  // 获取对象的赋值操作
  const result = Reflect.set(target, key, value, receiver);
  // 执行所有观察者
  queuedObservers.forEach((observer) => observer());
  // 执行赋值操作
  return result;
}
```

#### 省去复制原数组的方法

- Array.with()
- Array.toSorted()
- Array.toReversed()
- Array.toSpliced()

