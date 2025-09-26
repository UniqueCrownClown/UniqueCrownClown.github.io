---
title: 之马考核
date: 2022-10-21 11:23:51
categories: 知识笔记 #文章分类
tags: [zhima]
---

之马工场...

<!-- more -->

## 科目 1

1. ul 无序实心圆 ol 有序数字 子标签都是 li

2. jquery 入口函数

- $(function(){})

- $(document).ready(function(){})

```html
<body bgcolor="red">
  背景红色
</body>
```

单元格内容左对齐标签
浏览器状态栏消息 window.status="put you message here"

li 加图像标志 list-style-image

电子邮件链接
mailto:
发送表单数据 get 和 post

指定元素后用 after

clear 不能清除 position: absolute 只能是 float

文档结束加载之前用$(document).ready()

样式表标签 style 和 link
跳转新页面
window.location.replace("页面地址")
“window.location.href = "页面地址"

## 科目 2

js 基本 dom 操作

```js
var apple = document.createElement("input");
var appleText = document.createTextNode("hellowrold");
apple.style.width = "400px";
apple.style.height = "50px";
apple.style.marginTop = "100px";
apple.style.backgroundColor = "blue";
apple["type"] = "text";
console.log(apple.getAttribute("type"));
apple.value = "请输入你的手机号";
document.body.appendChild(apple);
```

document.createElement('')
document.getElementById
document.getElementsByClassName
document.getElementsByTagName
document.getElementByName
document.getAttribute()
document.setAttribute("type","text");

使用 js 完成隔行换色

```js
var lis = document.querySelectorAll("ul li");

for (var i = 0; i < lis.length; i++) {
  if (i % 2 === 0) {
    lis[i].style.backgroundColor = "black";
  } else {
    lis[i].style.backgroundColor = "white";
  }
}
```

垂直居中

```
.container {
    display:flex;
    justify-content: center;
    align-item: center;
}

{
    position: relative;
    position:absolute;
    top: 50%;
    margin-top: - half of self-height;
}
```

圆角
border-radius: 上左 x 上右 x 下右 x 下左 x / 同前四个 y

```
border-radius: 50%; // 椭圆
```

1. 写个表单
2. 写个表格
3. 表单验证弹窗
5. text 的样式
