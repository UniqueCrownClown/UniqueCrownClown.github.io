---
title: interview Guide
date: 2022-01-01 11:23:51
categories: interview
tags: [interview]
---

interview Guide...

<!-- more -->

#### http

multipart/form-data：可以上传文件或者键值对，最后都会转化为一条消息
x-www-form-urlencoded：只能上传键值对，而且键值对都是通过&间隔分开的,例如:name=张三&age=18

(http)[https://juejin.cn/post/6844904100035821575#heading-98]

### vue

vue 内部实现双向绑定过程：简单来说就是初始化 data 的时候会调用 observe 方法给，data 里的属性重写 get 方法和 set 方法，到渲染真实 dom 的时，渲染 watcher 会去访问页面上使用的属性变量，给属性的 Dep 都加上渲染函数，每次修改数据时通知渲染 watcher 更新视图
(vue 源码)[https://zhuanlan.zhihu.com/p/419896443]

#### vue 组件的 data 为什么需要时 function

- 因为 vue 组件是一个对象，对象是引用类型，所以当我们修改 data 中的数据时，所有引用 data 的地方都会被修改。
- 所以我们需要给 data 中的数据返回一个新的对象，这样就不会影响到其他地方。

#### vue diff

- v2 双端 diff 算法
  同时对新旧两组子节点的两个端点进行比较
- v3 快速 diff 算法
  预处理+基于最长子序列的移动优化

#### vue 的 keep-alive 原理以及生命周期

### react

### 手写简单算法

- js 模拟 hash 表实现

- 数组压栈出栈判断内容是否有效内容

```ts
function brackets(str) {
  const arr = [];
  const map = {
    "{": "}",
    "[": "]",
    "(": ")",
  };
  for (let i = 0, l = str.length; i < l; i++) {
    let s = str[i];
    if (map[s]) {
      arr.push(s);
    } else if (map[arr.at(-1)] === s) {
      arr.pop();
    }

    // 不满足出栈也不满足出栈时，表示不合法
    else {
      return false;
    }
  }
  return arr.length === 0;
}
```

- vue2 数据劫持

```js
function defineReactive(data) {
  if (!data || Object.prototype.toString.call(data) !== "[object Object]")
    return;
  for (let key in data) {
    let val = data[key];
    Object.defineProperty(data, key, {
      enumerable: true,
      configurable: true,
      get: function () {
        track(val, key);
        return val;
      },
      set: function () {
        trigger(val, key);
      },
    });
    if (typeof val === "object") {
      defineReactive(val);
    }
  }
}
function trigger(val, key) {
  console.log("sue set", val, key);
}
function track(val, key) {
  console.log("sue set", val, key);
}

const data = {
  name: "better",
  friends: ["1", "2"],
};
defineReactive(data);
```

- vue3 proxy

```js
function reactive(obj) {
  const handler = {
    get(target, prop, receiver) {
      track(target,prop);
      const value = Reflect.get(...arguments);
      if(typeof value === 'object'){
        reactive(value)
      }else{
        return value
      }
    },
    set(target, key, value, receiver) {
      trigger(target,key,value);
      return Reflect.set(...arguments)
    },
  };
  function track(data,key) {
    console.log("sue set",data,key)
  }
  function trigger(data,key,value) {
    console.log("sue set",key,":",value)
  }
}

const dinner = {
  name:'haochi1';
}
const proxy = reactive(dinner)
proxy.name
proxy.list = []
proxy.list.push(1)
```

- call,apply,bind

```js

```

- 柯里化

解 1(有点难懂看解 2)

```js
function add(a, b, c) {
  return a + b + c;
}
add(1, 2, 3);
const curryAdd = curry(add);
curryAdd(1)(2)(3);

function curry(fn) {
  let judge = (...args) => {
    if (args.length === fn.length) return fn(...args);
    return (...arg) => judge(...args, ...arg);
  };
  return judge;
}
```

解 2

```js
function curry(fn, currArgs) {
  return function () {
    let args = [].slice.call(arguments);
    if (currArgs !== undefined) {
      args = args.concat(currArgs);
    }
    // 递归调用
    if (args.length < fn.length) {
      return curry(fn, args);
    }
    // 递归出口
    return fn.apply(null, args);
  };
}
```

- instanceof

```js
function myInstanceof(A, B) {
  // 遍历链表
  let p = A;
  while (p) {
    p = p.__proto__;
    // B的prototype属性是否出现在A实例对象的原型链上
    if (p === B.prototype) {
      return true;
    }
  }
  return false;
}
function Foo() {}
var f = new Foo();
console.log(myInstanceof(f, Foo)); // true
console.log(myInstanceof(f, Object)); // true
console.log(myInstanceof([1, 2], Array)); // true
console.log(myInstanceof({ a: 1 }, Array)); // false
console.log(myInstanceof(Array, Object)); // true
console.log(Array instanceof Object); // true
```

- 手写 new 关键字

```js
// new关键字可以创建对象实例
// 产生一个新的对象
function myNew(fn, ...args) {
  const obj = new Object();
  obj._proto_ = fn.prototype;

  // Object.create()方法创建一个新对象，使用现有的对象来提供新创建对象的_proto_
  // const obj = Object.create(fn.prototype)

  // 执行fn并把fn中的this指向新创建的对象
  let res = fn.apply(obj, args);
  // 判断构造函数的返回值是不是object,是object则使用return的对象,不是的话就使用生成的对象
  return typeof res === "object" ? res || obj : obj;
}
let Dog = (name)=>{name,kind:"animals"}；
let dog = myNew(Dog,"dog")
```

- 链表

```js
class Node {
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}
// 链表类
class SinglyLinkedList {
  constructor() {
    this.head = new Node(); //head指向头节点
  }
  // 在链表组后添加节点
  add(data) {
    let node = new Node(data);
    let current = this.head;
    while (current.next) {
      current = current.next;
    }
    current.next = node;
  }
  // 添加到指定位置
  addAt(index, data) {
    let node = new Node(data);
    let current = this.head;
    let counter = 1;
    while (current) {
      if (counter === index) {
        node.next = current.next;
        current.next = node;
      }
      current = current.next;
      counter++;
    }
  }
  //删除某个位置的节点
  removeAt(index) {
    let current = this.head;
    let counter = 1;
    while (current.next) {
      if (counter === index) {
        current.next = current.next.next;
      }
      current = current.next;
      counter++;
    }
  }
  //反转链表
  reverse() {
    let current = this.head.next;
    let prev = this.head;
    while (current) {
      let next = current.next;
      //反转：改变当前节点指针。若当前节点是第一个（即头节点后面的）节点，
      //则此节点的next为null，否则next指向他的上一个节点
      current.next = prev === this.head ? null : prev;
      prev = current;
      current = next;
    }
    this.head.next = prev;
  }
}
```

- 二叉树深度遍历

1. 前序遍历
   先输出父结点，再左结点，最后右结点（父左右）

2. 中序遍历
   先输出左结点，再父结点，最后右结点（左父右）

3. 后序遍历
   先左结点，再右结点，最后父结点（左右父）

```js
// 前序
var Travelsal = function (root) {
  const res = [];
  const stack = [];
  while (root || stack.length) {
    while (root) {
      stack.push(root);
      res.push(root.val);
      root = root.left;
    }
    root = stack.pop();
    root = root.right;
  }
  return res;
};
```

```js
// 中序
const inorder2 = (root) => {
  if (!root) return;
  const res = [];
  const track = [];
  while (track.length || root) {
    while (root) {
      track.push(root);
      root = root.left;
    }
    root = track.pop();
    res.push(root.val);
    // 遍历根节点左边的节点的右侧
    root = root.right;
    console.log("root", root);
  }
};
```

```js
// 后序
var postorderTraversal = function (root) {
  // 初始化数据
  const res = [];
  const stack = [];
  while (root || stack.length) {
    while (root) {
      stack.push(root);
      res.unshift(root.val);
      root = root.right;
    }
    root = stack.pop();
    root = root.left;
  }
  return res;
};
```

- 二分查找

```js
const search = (nums, target) => {
  let i = 0;
  let j = nums.length - 1;
  let midIndex = 0;
  while (i <= j) {
    midIndex = Math.floor((i + j) / 2);
    const midValue = nums[midIndex];
    if (midValue === target) {
      return midIndex;
    } else if (midValue < target) {
      i = midIndex + 1;
    } else {
      j = midIndex - 1;
    }
  }
  return -1;
};
console.log(search([-1, 0, 3, 5, 9, 12], 9)); //4
```

- 快排(还没看)

```js
var sortArray = function (nums) {
  let left = 0,
    right = nums.length - 1;
  main(nums, left, right);
  return nums;
};
var main = function (nums, left, right) {
  if (nums.length === 1) {
    return;
  }
  // 获取left指针，准备下一轮分解
  let index = partitition(nums, left, right);
  if (left < index - 1) {
    // 继续分解左边数组
    main(nums, left, index - 1);
  }
  if (index < right) {
    // 分解右边数组
    main(nums, index, right);
  }
};
// partition算法使用头尾两个方向相反的指针进行遍历，先将数组第一个元素设置为比较元素，头指针从左至右找到第一个大于比较元素的数，尾指针从右至左找到第一个小于比较元素的数，全部交换完毕后将比较元素放到中间位置。
// 数组分解函数。
function partition(nums, left, right) {
  // 选取中间项为参考点。
  let pivot = nums[Math.floor((left + right) / 2)];
  // 循环直到left > right。
  while (left <= right) {
    // 持续右移左指针直到其值不小于pivot。
    while (nums[left] < pivot) {
      left++;
    }
    // 持续左移右指针直到其值不大于pivot。
    while (nums[right] > pivot) {
      right--;
    }
    // 此时左指针的值不小于pivot，右指针的值不大于pivot。
    // 如果left仍然不大于right。
    if (left <= right) {
      // 交换两者的值，使得不大于pivot的值在其左侧，不小于pivot的值在其右侧。
      [nums[left], nums[right]] = [nums[right], nums[left]];
      // 左指针右移，右指针左移准备开始下一轮，防止arr[left]和arr[right]都等于pivot然后导致死循环。
      left++;
      right--;
    }
  }
  // 返回左指针作为下一轮分解的依据。
  return left;
}
(快速排序)[https://zhuanlan.zhihu.com/p/268901123?ivk_sa=1024320u]
```

- class 和 object 和 function 的相互转化

- 虚拟 dom 转真实 dom

```js
const vnode = {
  tag: "DIV",
  attrs: {
    id: "app",
  },
  children: [
    {
      tag: "SPAN",
      children: [
        {
          tag: "A",
          children: [],
        },
      ],
    },
    {
      tag: "SPAN",
      children: [
        {
          tag: "A",
          children: [],
        },
        {
          tag: "A",
          children: [],
        },
      ],
    },
  ],
};

function render(vnode) {}
```

- 有效括号

思路:

1. 长度为偶数
2. 右括号前面，必须是相对应的左括号，才能抵消，才是有效括号
3. 定义一个全局变量数组，循环通过 push 和 pop 最后是否置空来判断是否有效括号
4. 如果是左括号就压入栈
5. 如果是右括号，弹出栈顶，两者对应则抵消，如果最后所有括号不能全部抵消则返回 false

```js
var isValid = function(s) {
    let stack = [];
    //获取字符串中的当前符号
    for(let i=0;i<s.length;i++) {
        const start = s[i];
        //如果是开头的符号则先入栈,如果是结尾的符号,就要和栈顶的符号做比较,看能不能变成一对
        if(s[i] == '('||s[i]=='{'||s[i]=='[') {
            stack.push(s[i]);
        }else{
        //栈顶元素如果是符号开头,和当前遍历的符号如果是一对儿,则栈顶元素pop出去
            const end = stack[ stack.length-1];
            if(start == ')'&& end=='(' ||
            start == '}'&& end=='{' ||
            start == ']'&& end=='[') {
                stack.pop();
            }else{
                return false;
            }
)
        }
    }
    return stack.length == 0;
}
```
