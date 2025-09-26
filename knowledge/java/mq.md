---
title: 消息队列
date: 2019-08-21 11:23:51
categories: java #文章分类
tags: [backend] #文章标签，可以一次添加多个标签
---

消息队列...

<!-- more -->

# 消息队列

## rabbitMQ

- virtual host
- exchange
- queue

### Work Queues

任务模型 让多个消费者绑定到一个队列，共同消费队列中的消息。

### 交换机类型

- Fanout 广播
- Direct 定向
- Topic 话题

### spring-boot-starter-amqp

```
@Bean
public MessageConverter messageConverter(){
    // 1.定义消息转换器
    Jackson2JsonMessageConverter jackson2JsonMessageConverter = new Jackson2JsonMessageConverter();
    // 2.配置自动创建消息id，用于识别不同消息，也可以在业务中基于ID判断是否是重复消息
    jackson2JsonMessageConverter.setCreateMessageIds(true);
    return jackson2JsonMessageConverter;
}

private final RabbitTemplate rabbitTemplate;

rabbitTemplate.convertAndSend(exchangeName, routeKey, message);

@RabbitListener(queues = "queueName")


```

### mq 高级

- 发送者可靠性

- mq 可靠性

- 消费者可靠性

如果某个消费者挂掉（信道、链接关闭或者 tcp 链接丢失）且没有发送 ack 应答，RabbitMQ 会认为该消息没有被处理完全然后会将其重新放置到队列中。通过这种方式你就可以确保消息永不丢失，甚至某个工作进程偶然挂掉的情况

- 延迟消息

## 阿里 rocketMQ

启动 RocketMQ 的顺序是先启动 NameServer，然后再启动 Broker;

1.  start salve 开启主从复制 + mysql 的读写分离，方案(MyCat??)
2.  分库分表
3.  aop 日志日志记录、性能监控、埋点上报、异常处理 AspectJ 的使用
