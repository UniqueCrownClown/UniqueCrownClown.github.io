---
title: lock
date: 2022-08-21 11:23:51
categories: java #文章分类
tags: [lock] #文章标签，可以一次添加多个标签
---

lock...

<!-- more -->

### ReetrantLock(雷安春特洛克)

### Synchronized(星坤奶日)

### redis 分布式锁

setnx key value：往 Redis 中写入一个 K-V 值。不过与普通的 set 指令不同的是：setnx 只有当 key 不存在时才会设置成功，当 key 已存在时，会返回设置失败。同时因为 redis 对于客户端的指令请求处理时，是使用 epoll 多路复用模型的，所以当同时多条线程一起向 redis 服务端发送 setnx 指令时，只会有一条线程设置成功。最终可以依赖于 redis 这些特性实现分布式锁。

做分布式锁的时候记得要设置过期时间，不然可能死锁

### zookeeper 分布式锁

Redis 数据不是实时同步的，主机写入成功后会立即返回，存在主从架构锁失效问题。
Zookeeper 数据是实时同步的，主机写入后需一半节点以上写入成功才会返回。
所以如果你的项目追求高性能，可以放弃一定的稳定性，那么推荐使用 Redis 实现。比如电商、线上教育等类型的项目。
但如果你的项目追求高稳定，愿意牺牲一部分性能换取稳定性，那么推荐使用 Zookeeper 实现。比如金融、银行、政府等类型的项目。

### Mysql 共享锁和排他锁

### 悲观锁或者乐观锁

数据库自身解决并发的两种策略

1. 悲观锁 每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁

2. 乐观锁 每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和 CAS 算法实现。

mybatis-plus 集成乐观锁的使用

在实体类中使用@Version 注解

```java
@Version
private Integer version;

```

在启动类中向容器放入乐观锁的拦截器

```java
@Bean
public MybatisPlusInterceptor optimisticLockerInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    return interceptor;
}

```

### 经典场景

库存超卖
