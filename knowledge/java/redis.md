---
title: redis
date: 2019-08-21 11:23:51
categories: java #文章分类
tags: [redis] #文章标签，可以一次添加多个标签
---

redis...

<!-- more -->

## redis 数据类型

1. 字符串 string
2. 哈希 hash
3. 列表 list
4. 集合 set
5. 有序集合,关联一个 double 类型的分数,可重复 zset

## redis-cli 常用命令

```
KEYS *
EXISTS key
TYPE key
TTL key #获取key的过期时间

SET key value
GET key
DEL key
SETEX key seconds value #设置key-value，并设置过期时间
SETNX key value #如果key不存在，则设置key-value (分布式锁)

HSET key field value #设置哈希表字段的值
HSETNX key field value #如果字段不存在，则设置字段的值
HGET key field #获取哈希表字段的值
HGETALL key #获取哈希表所有字段的值
HDEL key field #删除哈希表字段
HVALUE key #获取哈希表字段的数量
HKEYS key #获取哈希表所有字段
HVALS key #获取哈希表所有字段的值
HGETALL key #获取哈希表所有字段的值

LPUSH key value #在列表头部插入值
LRANGE key start stop #获取列表指定区间的值
LREM key count value #删除列表中指定值的数量
RPOP key #删除列表最后一个元素并返回值
LLEN key #获取列表长度
BRPOP key timeout #删除列表最后一个元素并返回值,没有元素会阻塞直到超时时间或发现可弹出元素

SADD key value #添加元素到集合
SMEMBERS key #获取集合所有元素
SREM key value #删除集合元素
SCARD key #获取集合元素数量
SINTERS key other_key #获取两个集合的交集
SUNIN key other_key #获取两个集合的并集
SDIFF key other_key #获取两个集合的差集

ZADD key score member #添加元素到有序集合或者更新元素分数
ZRANGE key start stop [withscores] #获取有序集合指定区间的元素
ZREM key member #删除有序集合元素
ZINCRBY key member value #有序集合元素增加member的分数

```

## java 操作

1. Jedis
2. Spring Data Redis
3. spring-boot-starter-data-redis

## 场景

redis 的 key-value 长度限制都为 512M

Redis 会自动删除过期的键。当键到达过期时间时，Redis 会将该键从数据库中删除。这个过程是自动的，无需手动删除。

当 Redis 中的 key 过期时，会自动从内存中删除。在 Redis 中，可以通过设置过期时间来让一个 key 在指定时间后自动过期。当 key 过期时，它将被自动删除，并释放其占用的内存空间。需要注意的是，Redis 的过期检查是惰性的，即 Redis 不会主动检查过期的 key，而是在 key 被访问时才会检查其是否过期。因此，在 Redis 中设置过期时间时需要考虑到这一点。

为了保证 Redis 的高可用，都会采用主从复制架构做读写分离，从而提升 Redis 整体的吞吐量以及可用性。

缓解数据库读写压力

存储登录信息 token

LPUSH 时间复杂度 O(1) 常数级复杂度，执行次数和数据规模无关
ZADD 时间复杂度 O(M\*log(n)) 对数级复杂度，执行次数和数据规模是对数关系

## 缓存雪崩缓存击穿和缓存穿透

Redis 的多级缓存架构

## 资料

[Redis 官方文档](https://redis.io/docs/)
[Redis 中文](https://redis.net.cn/order/)
