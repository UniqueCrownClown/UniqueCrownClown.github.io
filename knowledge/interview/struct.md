---
title: struct
date: 2022-01-01 11:23:51
categories: interview
tags: [interview]
---

struct Guide...

<!-- more -->

## all

- 队列 先进先出，后进后出。(FIFO first in first out)
- 栈 后进先出，先进后出 （LIFO）
- 数组 数组是内存中的连续存储区域，分成若干等分的小区域（每个区域大小是一样的）元素存在索引。特点：查询元素快（根据索引快速计算出元素的地址，然后立即去定位），增删元素慢（创建新数组，迁移元素）
- 链表 元素不是内存中的连续区域存储，元素是游离存储的，每个元素会记录下个元素的地址 特点：查询元素慢，增删元素快（针对于首尾元素，速度极快，一般是双链表）
- 树 
- 二叉树 
- 满二叉树
- 红黑树
- 堆



## 二叉树

1. 满二叉树 所有的叶子结点都在最后一层
2. 完全二叉树 叶子结点只可能在层次最大的两层上出现。对于最大层次的叶子结点，都依次排在最左边的位置上
3. 二叉排序树(二叉搜索树) 可为空树，左子树所有节点<根节点<右子树，不存在值相等的节点
4. 平衡二叉树 树上任意结点的左子树和右子树的深度只差不超过 1
5. 红黑树 节点是红色或者是黑色的,叶子节点（叶子是 NIL 节点）和根节点是黑色的,每个红色节点必须有两个黑色节点（从叶子到根节点的所有简单路径上不可能有两个连续的红色节点）,从任一节点到其每个叶子的所有简单路径都饱和相同数目的节点

**二叉搜索树的中序遍历是升序序列**

一棵二叉树是平衡二叉树，当且仅当其**所有子树也都是平衡二叉树**，因此可以使用递归的方式判断二叉树是不是平衡二叉树，递归的顺序可以是自顶向下或者自底向上。

- 二叉树遍历

  二叉树的**深度优先遍历**的**非递归**的通用做法是采用**栈**，**广度优先遍历**的**非递归**的通用做法是采用**队列**。

- 广度优先遍历
  又叫层次遍历，从上往下对每一层依次访问，在每一层中，从左往右（也可以从右往左）访问结点，访问完一层就进入下一层，直到没有结点可以访问为止。又叫层次遍历，从上往下对每一层依次访问，在每一层中，从左往右（也可以从右往左）访问结点，访问完一层就进入下一层，直到没有结点可以访问为止。

  保留全部结点，占用空间大； 无回溯操作(即无入栈、出栈操作)，运行速度快。
  广度优先搜索算法，一般需存储产生的所有结点，占用的存储空间要比深度优先搜索大得多，因此，程序设计中，必须考虑溢出和节省内存空间的问题。但广度优先搜索法一般无回溯操作，即入栈和出栈的操作，所以运行速度比深度优先搜索要快些。

  ```js
  // dom广度优先遍历
  let widthTraversal2 = (node) => {
    let nodes = [];
    let stack = [];
    if (node) {
      stack.push(node);
      while (stack.length) {
        let item = stack.shift();
        let children = item.children;
        nodes.push(item);
        for (let i = 0; i < children.length; i++) {
          stack.push(children[i]);
        }
      }
    }
    return nodes;
  };
  ```

- 深度优先遍历
  不全部保留结点，占用空间少；有回溯操作(即有入栈、出栈操作)，运行速度慢。
  通常深度优先搜索法不全部保留结点，扩展完的结点从数据库中弹出删去，这样，一般在数据库中存储的结点数就是深度值，因此它占用空间较少。所以，当搜索树的结点较多，用其它方法易产生内存溢出时，深度优先搜索不失为一种有效的求解方法。

```js
// 非递归
let deepTraversal3 = (node) => {
  let stack = [];
  let nodes = [];
  if (node) {
    // 推入当前处理的node
    stack.push(node);
    while (stack.length) {
      let item = stack.pop();
      let children = item.children;
      nodes.push(item);
      for (let i = children.length - 1; i >= 0; i--) {
        stack.push(children[i]);
      }
    }
  }
  return nodes;
};
```

(深度优先和广度优先实现)[https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/9]

## 二分查找

```js
function binarySearch(arr, target) {
  let start = 0;
  let end = arr.length - 1;
  if (!end) {
    return arr[0] === target ? 0 : -1;
  }
  if (end == 1) {
    return arr[0] === target ? 0 : arr[1] === target ? 1 : -1;
  }
  let middle;
  while (start <= end) {
    middle = ((start + end) / 2) | 0; // 向下取整
    if (arr[middle] === target) {
      return middle;
    } else if (target > arr[middle]) {
      start = middle + 1;
    } else {
      end = middle - 1;
    }
  }
}
```

## 哈希表

动态扩容
哈希函数
map 和 set
冲突解决
lru(latest recently used) 算法

## 常见排序算法

## 时间和空间复杂度

1. 找出数组两数之和等于 target 的组合

```js
var twoSum = function (nums, target) {
  for (let i = 0; i < nums.length - 1; i++) {
    for (let j = i + 1; j < nums.length; j++) {
      if (num[i] + num[j] === target) {
        return [nums[i], nums[j]];
      }
    }
  }
};
```

时间复杂度---O(n2)

```js
var twoSum = function(nums, target) {
let res = {};
for( let i = 0;i < nums.length; i++){
    res[target- nums[i]] = nums[i];
}
 for (let j = 0; j < nums.length; j++) {
     <!-- if(nums[j] === res[j]){
         return [nums[j],target-num[j]]
     } -->
     if(res[num[j]] !== undefined){
         return [nums[j], res[nums[j]]]
     }
 }

}
```

时间复杂度--- O(n)
空间复杂度--- O(n)

## 分治法

1. 递归和迭代的区别

迭代：利用已知的变量值，不断用变量的旧值递推新值，直到到达结束状态。

递归：函数直接或间接调用函数自身，直到满足终止条件，再逐层回归。

迭代：迭代是环结构，从初始状态开始，每次迭代都遍历这个环，并更新状态，多次迭代直到到达结束状态。

递归：递归是树结构，从字面可以理解为重复“递推”和“回归”的过程，当“递推”到达底部时就会开始“回归”，其过程相当于树的深度优先遍历。

迭代与普通循环的区别：迭代时，循环代码中参与运算的变量同时是保存结果的变量，当前保存的结果作为下一次循环计算的初始值；

递归与普通循环的区别：循环是有去无回，而递归则是有去有回(因为存在终止条件)。

## 动态规划

## 简单归纳法

递推法的基本思想: 分析问题, 归纳出 递推式
递推法的实现---for 循环(数组)

## 贪心算法

贪心算法是一种在每一步选择中都采取当前状态下最优的选择，从而希望导致结果是全局最优的算法。它通常用于解决一些最优化问题，如最小生成树、最短路径等。贪心算法的优点是简单、高效，但缺点是不能保证得到全局最优解。

## 回溯法

深度优先
