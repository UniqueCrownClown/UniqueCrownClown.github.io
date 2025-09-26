---
title: jvm笔记
date: 2017-08-21 11:23:51
categories: java #文章分类
tags: [java] #文章标签，可以一次添加多个标签
---

jvm...

<!-- more -->

## 常见jdk工具

jps 列出当前运行的java进程

jstat 列出jvm的运行状态

jinfo 列出jvm的配置信息

jmap 列出jvm的堆内存信息，生成heapdump文件

```
jmap -dump:format=b,file=E:/javaspace/testa-mat/heapdump.hprof <PID>
```

jhat 用于分析heapdump文件 建立一个web服务器 在浏览器查看结果

jstack 列出jvm的线程信息

## 线程安全问题


