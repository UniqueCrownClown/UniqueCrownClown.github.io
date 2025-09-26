---
title: jstrick
date: 2017-10-20 11:23:51
categories: js #文章分类
tags: [jstrick] #文章标签，可以一次添加多个标签
---

jstrick...

<!-- more -->

### 20200506

1. if(string in Object)的用法,等于 Reflect.has(Object,string)

```js
var man = {
  name: "小明",
  age: 18,
};

if ("age" in man) {
  console.log("man对象中含有age属性");
} else {
  console.log("没");
}
```

2. 数组转对象

```js
const apple = true;
const arr = apple ? [1, 2, 3] : [];
const obj = { ...arr };
console.log(obj);
// Output: {0: 1, 1: 2, 2: 3}
```

3. 短路运算

```js
if (isOnline) {
  postMessage();
}
// 使用&&
isOnline && postMessage();

// 使用||
let name = null || "森林";
```

4. for ..in 循环

由于原型继承，我们现在有两种类型的属性被混淆了：存在于对象本身的属性，即它自己的属性，以及存在于原型链的属性，即继承的属性。

我们都知道 for...in 会遍历原型链上的属性，所以一般会结合 **hasOwnProperty** 来判断属性是否在对象自身上，而不是在原型链上。

```js
for (let key in obj) {
  if (Object.prototype.hasOwnProperty.call(obj, key)) {
    // ...
  }
}
```

对象遍历替代方案:
用 Object.keys 或者 Object.entries 转成数组就可以用数组方法遍历了，而且遍历的是自身属性，不会遍历到原型上。

```js
Object.keys(obj).forEach((key) => {
  // ...
});

Object.entries(obj).forEach(([key, val]) => {
  // ...
});
```

不想遍历整个对象，数组方法不能用 break 终止循环

```js
// 巧妙的利用find来中止循环
Object.keys(obj).find((key) => key === "name");
```

tips： 用字面量定义对象，而不是用 new Object 的方式

5. try/catch

- try/catch 只能捕获到同步的异常，不能捕获语法和异步的异常
- promise 内部的无论是 reject 或者 throw new Error，都可以通过 catch 回调捕获。

```js
try {
  throw new Error(3);
} catch (e) {
  console.log(e);
}

try {
  setTimeout(function () {
    throw new Error(5);
  }, 100);
} catch (e) {
  console.log(e);
}
// 如果有异步函数的话，就不需要用try catch 了， 直接用Promise就好了
var promise = new Promise(function (resolve, reject) {
  setTimeout(function () {
    throw new Error(6);
  }, 100);
});
promise.then(
  (v) => console.log("resolve:" + v),
  (e) => console.log("reject:" + e)
);
```

参考错误的统一处理和 koa 的错误的统一处理
[https://www.jb51.net/article/160260.htm](https://www.jb51.net/article/160260.htm)

6. null 和 undefined 和空串的区别(所有数据类型都是 Object,初始值为 null)

### 20210917

1. 空值合并运算符??(es2020)
   空值合并运算符??提供一种简短的语法来获取列表中第一个'已定义'的变量(即值不是 null 或 undefined 的变量)

```js
x = a ?? b
x = （a !== null && a !== undefined）? a : b；
```

与||比较

- ||返回第一个真值，??返回第一个已定义的值（区别是 0 的情况视为假值）
- ??运算符的优先级非常低，只略高于?和=，，参与二则运算时需要加空号
- 如果没有明显添加括号，，不能将其与 || 或 && 一起使用

2. 链判断运算符 '?.'

- 获取一个对象的多层属性值的时候，，需要进行多次判断，不判断，一个为空则报错，导致后面无法继续进行(短路机制)

```js
// error
const firstName = message.body.user.firstName;
// ok
const firstName =
  (message &&
    message.body &&
    message.body.user &&
    message.body.user.firstName) ||
  "default";
// replace
const firstName = message?.body?.user?.firstName || "default";
```

在链式调用的时候判断，左侧的对象是否为 null 或 undefined。如果是，就不再往下运算，直接返回 undefined

3. 管道运算符优化柯里化式的多层 function 调用

```js
const result = foo(bar(baz("hello world")));
// 从上到下，参数输入，结果输出，很简洁
const result = "hello world" |> baz |> bar |> foo;
```

4. 解构

- 解构的重命名和嵌套结构

ES6 的解构赋值虽然好用。但是要注意解构的对象不能为 undefined、null。否则会报错，故要给被解构的对象一个默认值。

```js
const obj = {
  name: "林三心",
  doing: {
    morning: "摸鱼",
    afternoon: "摸鱼",
    evening: "sleep",
  },
};
// 1.解构重名
const { name: myname } = obj || {};
console.log(myname); // 林三心
// 2. 嵌套解构
const {
  doing: { evening },
} = obj;
console.log(evening); // sleep
// 3. 解构使用默认值
const {
  doing: { evening = "defaultValue" },
} = obj;
// 4. 多次解构
let obj = {
  part1: {
    name: "零一",
    age: 23,
  },
};

const {
  part1: { name, age },
} = obj;
console.log(part1); // Uncaught ReferenceError: part1 is not defined

const {
  part1: { name, age },
  part1,
} = obj;
console.log(part1); // {name: "零一", age: 23}
```

- 逗号参数解构赋值

```js
function foo(...args) {
  const [a, , c] = args;
  console.log(a, c); // 1,3
}
foo(1, 2, 3);
```

5. 判断空对象

```js
Object.keys({}).length === 0;
```

6. js 链式调用方法

在方法调用后返回 this 即可

```js
var user = function (name, age) {
  this.name = name;
  this.age = age;
};
user.prototype.getName = function () {
  console.log("姓名是", this.name);
  return this;
};
user.prototype.getAge = function () {
  console.log("年龄是", this.age);
  return this;
};
var user1 = new user("zjf", 22);
user1.getName().getAge();
//姓名是 zjf
//年龄是 22
```

7. 运用 es6 不用循环生成 m 长度都是 n 的数组

```js
var arr = Array.from({ length: 100 }, (v, k) => k);
alert(arr.join(","));
```

8. Object.assign 复制时，将对象的属性和方法当做普通属性来复制，并不会复制完整的描述信息，比如 this。

```js
const p1 = {
  a: "y",
  b: "d",
  get name() {
    return `${this.a} ${this.b}`;
  },
};
const p2 = Object.assign({}, p1);

p2.a = "z";

p2.name; // y d; 发现并没有修改p2.a的值，是因为this仍旧指向p1
```

使用 Object.getOwnPropertyDescriptors 获取完整描述信息

```js
const description = Object.getOwnPropertyDescriptors(p1);

const p2 = Object.defineProperty({}, description);

p2.a = "z";

p2.name; // z d
```

9. 有条件地向对象添加属性

```js
const condition = true;
const person = {
  id: 1,
  name: "dby",
  ...(condition && { age: 12 }),
};
```

10. 有条件地向数组添加元素

```js
module.exports = {
  plugins: [
    new HtmlWebpackPlugin(),
    isEnvProduction && new MiniCssExtractPlugin(),
    useTypeScript && new ForkTsCheckerWebpackPlugin(),
  ].filter(Boolean),
};
```

### 20211017

1. 一行超短代码 实现 "随机生成字符串" 的功能

```js
const str = Math.random().toString(36).substr(2, 10);
console.log(str); // 'w5jetivt7e'
```

先是 Math.random() 生成 [0, 1) 的数，也就是 0.123312、0.982931 之类的，然后调用 number 的 toString 方法将其转换成 36 进制的，按照 MDN 的说法，36 进制的转换应该是包含了字母 a~z 和 数字 0~9 的，因为这样生成的是 0.89kjna21sa 类似这样的，所以要截取一下小数部分，即从索引 2 开始截取 10 个字符就是我们想要的随机字符串了
很多开源库都使用此方式为 DOM 元素创建随机 ID。

2. 箭头函数 return 一个对象时省略 return 的写法,小括号包裹,避免歧义

```js
const a = (params) => ({ apple: 1, peach: params });
const result = a(2); //{apple:1，peach:2}
```

3. foreach 循环

利用 try catch 跳出循环

```js
function getItemById(arr, id) {
  var item = null;
  try {
    arr.forEach(function (curItem, i) {
      if (curItem.id == id) {
        item = curItem;
        throw Error();
      }
    });
  } catch (e) {}
  return item;
}
```

4. 枚举值用 1 << n 的好处

在编程中，使用 1 << n（其中 n 为非负整数）的方式可以快速计算 2 的 n 次方。例如，1 << 2 的结果为 4，即 2 的 2 次方。因此，使用 1 << n 可以使代码更加简洁、易读和高效。

当用于枚举时，使用 1 << n 可以将每个枚举值表示为 2 的幂，这样可以方便地进行位运算,使用按位或（|）和按位与（&）等操作来组合和检查权限

```typescript
enum Permission {
  READ = 1 << 0,   // 1
  WRITE = 1 << 1,  // 2
  EXECUTE = 1 << 2 // 4
};
int userPermissions = READ | WRITE; // 用户拥有读和写权限
if (userPermissions & EXECUTE) {
  // 用户拥有执行权限
} else {
  // 用户没有执行权限
}
```

5. Array.from (a.entries())用来 debug 的时候方便查看内容

### 20211018

1.  (0, fn)(args)这种形式调用函数

```js
window.name = "Gina";
const person = {
  name: "Alice",
  getName() {
    console.log(`My name is ${this.name}`);
  },
};

person.getName(); // My name is Alice
(0, person.getName)(); // My name is Gina
```

逗号操作符返回最后一个操作数的值
原理是，逗号表达式表示先执行左边，在执行右边，最终返回右边，(0, person.getName)相当于返回了 person.getName 的引用，相当于

```js
const getName = person.getName;
getName(); // My name is Gina
```

2. js 的类型检查

- JSDocs,单纯用注释,和代码解耦了。。
- 为了启用针对 JavaScript 文件的全面类型检查，您只需将注释// @ts-check 添加到待检查的 JavaScript 文件的顶部便可
- vscode 在 settings.json 文件中设置为"javascript.implicitProjectConfig.checkJs": true。
- 项目级别 tsconfig.json 文件配置{ "compilerOptions": { "checkJs": true,/_ Report errors in .js files. _/ } }

3. 代码重构
   代码升级重构时，对于比较大的改动，可以新写一个函数，并且给老函数打上类似 @Deprecated 的注释，表示已过时，引导用户去用新的。

4. 生成长度为 N 的数组

```js
// 生成长度为100的数组
const arrN = [...Array(100).keys()];
// [0,1,2,3,...,99]
```

5. 生成 A-Z 的数组

```js
const AZCodeArr = [...Array(91).keys()]
  .filter((i) => i > 64)
  .map((i) => String.fromCharCode(i));
//['A','B','C','D'...]
```

6. function 的原型方法

```js
// Function.prototype.arguments
function func1(a, b, c) {
  console.log(arguments[0]);
  // expected output: 1
  console.log(arguments[1]);
  // expected output: 2
  console.log(arguments[2]);
  // expected output: 3
}

func1(1, 2, 3);
// Function.prototype.name
const func1 = function () {};
const object = {
  func2: function () {},
};
console.log(func1.name);
// expected output: "func1"
console.log(object.func2.name);
// expected output: "func2"

// Function.prototype.length
function func1() {}
function func2(a, b) {}
console.log(func1.length);
// expected output: 0
console.log(func2.length);
// expected output: 2
```

7. 请求参数

- URLSearchParams

```js
// url: better.com?id=2&isShare=true
const urlSearchParams = new URLSearchParams(window.location.search);
const params = Object.fromEntries(urlSearchParams.entries());
console.log(params);
console.log(params.id);
```

- 正则表达式实现 1

```js
function getParams(url, params) {
  var res = new RegExp("(?:&|/?)" + params + "=([^&$]+)").exec(url);
  return res ? res[1] : "";
}
// url: better.com?id=2&isShare=true
const id = getParams(window.location.search, "id");
```

- 正则表达式实现 2

```js
// url中的所有查询参数可以通过 window.location.search字段获取，以字符串的形式返回。并有固定的格式 ?param1=value1&param2=value2···
function getUrlQueryParams(url) {
  var params = {};
  var regex = /[\?&]([a-zA-Z_]+)=([\w\-\.]*)/g;
  while ((results = regex.exec(url)) !== null) {
    params[results[1]] = results[2];
  }
  return params;
}
```

8. jsPlugin (js 插件)[http://geocld.github.io/2016/03/10/javascript_plugin/]

9. 实现一个 sleep 函数

```js
function sleep(time) {
  return new Promise((resolve) => setTimeout(resolve, time));
}
```

10. 通配符匹配

- ? 匹配任何单字符
- /\* 匹配 0 或者任意数量的字符
- /** 匹配 0 或者更多的目录
  /app/\*.x 匹配(Matches)所有在 app 路径下的.x 文件  
  /app/p?ttern 匹配(Matches) /app/pattern 和 /app/pXttern,但是不包括/app/pttern  
  /**/example 匹配(Matches) /app/example, /app/foo/example, 和 /example  
  /app/**/dir/file.匹配(Matches) /app/dir/file.jsp, /app/foo/dir/file.html,/app/foo/bar/dir/file.pdf, 和 /app/dir/file.java  
  /**/\*.jsp 匹配(Matches)任何的.jsp 文件

### 20211221

1. js 函数变量没有占位符语法,一般使用下划线

```js
const a = new Array(10).fill(null).map((...args) => args[1]);
const b = [...Array(10)].map((_, index) => index);

function test(...[, arg]) {
  console.log("as your wish: ", arg);
}
test(1, 2);
```

2. window.open 是一种常见的用于打开新页面的方法，通常用于弹窗下载功能的实现

3. for/in 与 for/of 有什么区别
   for/in 遍历的是数组的索引，而 for/of 遍历的是数组元素值
   for/in 会遍历数组所有的可枚举属性，包括原型
   for/of 不支持普通对象，想遍历对象的属性，可以用 for/in 循环，或者内建的 Object.keys()方法
   for/in 遍历对象；for/of 遍历数组

4. 正无穷和负无穷

Number.POSITIVE_INFINITY; // => Infinity
Number.NEGATIVE_INFINITY; // => -Infinity

5. js string 不能通过索引下标的方式修改指定字符

```js
const a = "asdf";
a[0] = "b";
console.log(a); // "asdf"
```

6. Number.prototype.toString()

```js
(3).toString(); //等价3.0.toString
(3)
  .toString()(
    //因为浮点数的数字和点和中间多了一个空格，也就不会将点解释为方法调用
    3
  )
  .toString(); //将数字用括号包起来，明确了点和数字的关系
(3)["toString"](); //不建议，很别扭
```

7. .filter(Boolean)

.filter(Boolean)方法用于在 JavaScript 中从数组中删除任何假值。当你想从数组中删除 null、undefined、0、false 和空字符串等值时，这个方法非常有用。该方法通过创建一个新数组，其中只包含原始数组中的真值来工作。

8. Function constructor

9. import { default as helloworld } from "modules"

10. 怎么使用条件断点,**打断点的时候鼠标右键**

### 20230315

#### js 的简单进制转换

1. 十进制转二进制

```js
var x = 10; // 此处应为数字
console.log(typeof x); // Number

console.log("8", x.toString(8)); //将十进制转换成8进制
console.log("16", x.toString(16)); //将十进制转换成16进制
console.log("32", x.toString(32)); //将十进制转换成32进制
```

2. 二进制转十进制

```js
var num1 = "100";
var num2 = parseInt(num1, 2); // 4
```

#### user-agent

限制 user-agent 的作用
限制某些特定的访问
比如禁止 curl 或者 Chorme（谷歌浏览器）访问网站。
限制不太重要的搜索引擎的蜘蛛爬虫

```js
if  ($http_user_agent ~*  'curl|baidu|11111' )
     {
         return  403;
     }
```

#### atuofit.js

### 调试技巧

如果你的页面因未知代码陷入了快速刷新的死循环，可在项目中添加以下以下代码；
页面刷新前会进入 debug 状态，在 devtools 中查看调用堆栈（call stack）即可了解刷新的原因

```js
window.addEventListener("beforeunload", () => {
  debugger;
});
```
