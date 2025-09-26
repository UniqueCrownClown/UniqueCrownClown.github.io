---
title: record
date: 2019-03-04 11:20:32
categories: js #文章分类
tags: [js杂烩] #文章标签，可以一次添加多个标签
---

dialy record...

<!-- more -->

### node 自动发邮件的实现

- 决定使用 node 上的一个插件叫 cheerio,配合 superagent 能够很方便地爬取网页上的信息。
- 发送邮件的 Node 插件 nodemailer,兼容主流的 Email 厂商，只需要配置好邮箱账号和 smtp 授权码，便可以用你的邮箱账号在 node 脚本上发文件

### npm-run-all

在根上定义安装多个子模块的 package 和执行多个子模块的 script

### express+mongo+vue+node=博客系统

mongodb+nosql(not only sql)
非关系型的数据存储

#### mongo

    mongod --config "D:\Mongo\mongo.conf"  --auth --install --serviceName "MongoDB"
    net start MongoDB
    mongod.exe --remove --serviceName "MongoDB"

    把mongoose理解为是用来操作mongoDB。 安装完后，我们在项目中引用mongoose，并连接数据库

    mongo默认端口-27017

#### 常见命令行

    net start MongoDB
    mongo起应用
    use blog(创建数据库blog)
    db.users.insert({"name":"taotao","email":"taotao163.com","age":"24"});(user表里插入一条数据)
    db.users.find();(查users表)
    show dbs;(显示所有数据库)

MongoDB 缺省是没有设置权限的，也就是说，没有设置访问权限。上面的启动方式就是没有设置权限的启动，所以，登录数据库，是不需要验证用户名和密码的。
要知道 MongoDB 服务器的端口，任何能访问到这台服务器的人都可以查询和操作 MongoDB 数据库的内容
加上 --auth 这样以后就可以使用用户名和密码后才能进行 MongoDB 的相关操作

数据库用户有两种，一种是管理员，用来管理用户，一种是普通用户，用来访问数据。

#### express

```shell
//全局安装express
npm install express -g

//安装express全局生成器，可以自动生成项目文件
npm install express-generator -g
```

在当前工作目录生成一个叫做 blog 的项目，并采用 ejs 模版

```shell
// 默认是jade模版， -e就是采用ejs模版
express -e blog
```

然后安装所有依赖：

```shell
// 默认是jade模版， -e就是采用ejs模版
express -e blog

//进入项目文件夹
cd blog

//安装所有依赖
npm install

//官方的方法，因该是调试模式,没深究
set DEBUG=blog $ npm start

//直接启动
npm start

//安装Mongoose,   --save把Mongoose模块加入到项目依赖
npm install mongoose --save

```

### nodejs+websocket+stomp

WebSocket 为我们提供了 Web 双向通信的通道，但对于消息的交互协议还需要我们来自己实现

stomp 文本传输协议，借助 Stomp 协议，可以很方便的实现一种"订阅-发布"的通用机制，这个就是非常具有竞争力的一个特性了。

activeMQ(JMS 规范的具体实现)(非常流行的开源消息服务器)
Broker 是 ActiveMQ 的一个实例。
放开端口号
web 控制台 8186 http://localhost:8161 默认登录账号 admin 密码 admin
消息服务 broker 连接端口：61616
消息传送模式
点对点(Point-to-Point)
客户端发消息到服务端，服务端处理后返回消息给原客户端（或者发送消息给指定客户端）
发布/订阅(Publish/Subscribe)
生产者发布基于某主题 topic
publisher.publish(topic, message);
所有订阅 topic 的消费者
subscriberA.subscribe(topic, function(message, headers) {
console.log('subscriberA From Topic:', message);
});
stompjs
//构建 Stomp 对象
publisher = new Stomp('127.0.0.1', 61613, '', '')
subscriberA = new Stomp(...)
Broker 的消息转发 1. 服务的位置透明性。 2. 组件的可变性及扩展性。由于 Server 是注册到 Broker 上的，所以 Server 可以动态的增加、删除、改变。 3. Broker 之间可交互。 4. 可重用性。 5. 由于组件的耦合度较小，调试和测试的工作也是可控的。

### forEach 到底可以改变原数组吗

一般在项目中数组操作的都是对象数组，而不是数字字符串数组,而 forEach 可以改变对象数组

#### 代码示例

1. 数字字符串数组的循环重赋值不生效

```js
const array = [1, 2, 3, 4];
array.forEach((ele) => {
  ele = ele * 3;
});
console.log(array); // [1,2,3,4]
```

2. 对象数组的循环赋值 1

```js
const objArr = [
  {
    name: "wxw",
    age: 22,
  },
  {
    name: "wxw2",
    age: 33,
  },
];
objArr.forEach((ele) => {
  if (ele.name === "wxw2") {
    ele.age = 88;
  }
});
console.log(objArr); // [{name: "wxw", age: 22},{name: "wxw2", age: 88}]
```

3. 对象数组的循环赋值 2

```js
const changeItemArr = [
  {
    name: "wxw",
    age: 22,
  },
  {
    name: "wxw2",
    age: 33,
  },
];
changeItemArr.forEach((ele) => {
  if (ele.name === "wxw2") {
    ele = {
      name: "change",
      age: 77,
    };
  }
});
console.log(changeItemArr); // [{name: "wxw", age: 22},{name: "wxw2", age: 33}]
```

4. 用索引实现数组子项修改

```js
// 基本类型可以欧~
const numArr = [33, 4, 55];
numArr.forEach((ele, index, arr) => {
  if (ele === 33) {
    arr[index] = 999;
  }
});
console.log(numArr); // [999, 4, 55]

// 引用类型也可以欧~
const allChangeArr = [
  {
    name: "wxw",
    age: 22,
  },
  {
    name: "wxw2",
    age: 33,
  },
];
allChangeArr.forEach((ele, index, arr) => {
  if (ele.name === "wxw2") {
    arr[index] = {
      name: "change",
      age: 77,
    };
  }
});
console.log(allChangeArr); // // [{name: "wxw", age: 22},{name: "change", age: 77}]
```

#### 总结

堆的特点是完全二叉树

基本类型我们当次循环拿到的 ele，只是 forEach 给我们在另一个地方复制创建新元素，是和原数组这个元素没有半毛钱联系的！所以，我们使命给循环拿到的 ele 赋值都是无用功
JavaScript 是有基本数据类型与引用数据类型之分的。对于基本数据类型：number,string,Boolean,null,undefined 它们在栈内存中直接存储变量与值。而 Object 对象的真正的数据是保存在堆内存，栈内只保存了对象的变量以及对应的堆的地址，所以操作 Object 其实就是直接操作了原数组对象本身。

forEach 的基本原理也是 for 循环，使用 arr[index]的形式赋值改变，无论什么就都可以改变了。

### js 的 class 小解

class 类本质上就是一个函数,自身指向的就是构造函数
class 类是构造函数的另一种写法,仍然存在 prototype 方法
可以通过原型 prototype 修改类方法和新增方法
可以通过 Object.assign 放大为对象动态增加方法
可以使用实例对象的\_\_proto\_\_属性新增类的方法
constructor 里的属性为实例属性,即定义在 this 对象上
除去实例属性都称为原型属性,即定义在 class 上
hasOwnProperty 判断属性是否实例属性
通过 in 操作符判断属性是否存在

```js
class Cat {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  Say() {
    return "my name is" + this.name;
  }
}
var cat1 = new Cat("有鱼", 2);
console.log(cat1.Say());

console.log(typeof Cat); // function
console.log(Cat.prototype.constructor === Cat); // true

Cat.prototype.Say = function(){
    return '我的名字是' + this.name+',我是原型prototype声明同样的Say方法，把原有Say方法覆盖了';
}
cat2 = new Cat('年年',5);
console.log(cat2.Say());//我的名字是年年，我是原型prototype声明同样的Say方法，把原有Say方法覆盖了
Cat.prototype.Go = function(){
    return return '我的年龄是' + this.age;
}
console.log(cat2.Go());//我的年龄是5

Object.assign(Cat.prototype,{
  Eat: function() {
    return this.name
  }
});
cat3 =new Cat('卡卡',4);
console.log(cat3.Eat());//卡卡

cat3.__proto__.Play = function(){
    return this.name;
}
console.log(cat3.Play());//卡卡
```

(没有 class 前 javascript 定义 class 的三种方法)[http://www.ruanyifeng.com/blog/2012/07/three_ways_to_define_a_javascript_class.html]

### map 和 object 的区分使用

object 的 Key 只能是字符串,Map 可以使用任何 JavaScript 数据类型作为键,Map 可以确保遍历的顺序和插入的顺序一致

性能差异:

- 给定固定大小的内存，Map 大约可以比 Object 多存储 50% 的键/值对。
- Map 插入性能略快，如果涉及大量操作，建议使用 Map。
- 如果代码涉及大量的删除操作，建议选择 Map。
- 查找速度的性能差异极小，但如果只包含少量键/值对，则 Object 有时候速度更快。Object 作为数组使用时浏览器会进行优化。如果涉及大量查找操作，选择 Object 会更好一些
