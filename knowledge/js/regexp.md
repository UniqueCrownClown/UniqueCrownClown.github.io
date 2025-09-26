---
title: 正则表达式
date: 2017-10-27 11:23:51
categories: js #文章分类
tags: [正则表达式] #文章标签，可以一次添加多个标签
---

正则表达式...

<!-- more -->

### 简单规则应用

模糊匹配：

\d：匹配一个数字（digit）
\D：匹配一个非数字
\w：匹配字母或数字
\W：匹配非字母且也是非数字的情况
\s：匹配一个空白字符，比如：空格、\n \r \t
\S：匹配一个非空白字符
[A-Z]：匹配大写字母
[a-z]：匹配小写字母
[a-zA-Z]：匹配大小写字母

尝试匹配多个：

+：匹配至少一个（可以匹配多个）； \*：匹配 0 个或者多个；
{n,m}：匹配 n 个或者 m 个之间的个数都行；
?：表示匹配一个或 0 个；

### String.prototype.search()

string.search(regular expression)
如果匹配成功，则 search() 返回正则表达式在字符串中首次匹配项的**索引**;否则，返回 -1

### RegExp.prototype.test()

返回布尔值
RegExp.text("testString")
RegExp => /expression/

### String.prototype.match()

string.match(regular expression)

返回一个字符串匹配正则表达式的**结果数组**

### RegExp.prototype.exec()

在一个指定字符串中执行一个搜索匹配。返回一个**结果数组或 null**。
