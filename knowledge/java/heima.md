---
title: b站黑马教程
date: 2019-08-21 11:23:51
categories: java #文章分类
tags: [backend] #文章标签，可以一次添加多个标签
---

b 站黑马教程...

<!-- more -->

# 知识点笔记

## 瑞吉外卖

1. ThreadLocal 线程本地变量，用于保存线程私有数据,以线程为隔离保存副本

每个线程只能访问自己的ThreadLocal变量。通过ThreadLocal为线程添加的值只能由这个线程访问到，其他的线程无法访问，因此就避免了多线程之间的同步问题

客户端的每次 http 请求,对应的服务端都会分配一个新的线程来处理

2. mybatis plus 的 save 方法会影响传入的实体类，给它注入 id

3. idea 操作-->表达式.var 快捷声明

4. IDWorker

5. String.valueOf(long)

6. AtomicInteger 高并发多线程安全

7. BigDecimal

8. swagger 增强使用 knife4j

9. 拦截器和过滤器的执行时序

拦截器基于 java 反射
过滤器基于 servlet 的函数回调

tomcat-->filter-->servlet-->interceptor-->controller-->service-->dao-->mapper-->db

## 苍穹外卖

1. 微信登录流程

手机号个人权限的小程序无法获取，要更高级别

wx.login-->code-->code2Session-->openid 存在数据库关联 user

2. useGenerateKeys="true" keyProperty="id" 插入后返回主键值

3. 微信支付

4. 内网穿透 cpolar/natapp

5. mybatis批量删除和批量更新和批量插入的实现


## 黑马头条

1. inner join & left join & right join

2. freemarker 静态模板技术

3. minio 分布式文件系统

## 学习资料

(微服务框架)[https://b11et3un53m.feishu.cn/wiki/UMgpwmmQKisWBIkaABbcwAPonVf]