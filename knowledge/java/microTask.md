---
title: 微服务
date: 2019-08-21 11:23:51
categories: java #文章分类
tags: [backend] #文章标签，可以一次添加多个标签
---

微服务...

<!-- more -->

## 微服务通信

spring cloud 微服务之间尽量解耦，，微服务之间的通信方法
两种方式(Ribbon+RestTemplate, 另外一种是 Feign)

@EnableDiscoveryClient //表示 eureka 客户端(等于@EnableEurekaClient)
@LoadBalanced//在注册中心里进行查找微服务,负载均衡
a. 启动类中添加
注册 RestTemplate
@Bean
@LoadBalanced//在注册中心里进行查找微服务,负载均衡
public RestTemplate restTemplate(){

    	RestTemplate restTemplate=new RestTemplate();
    	return  restTemplate;
    }

    restTemplate.getForObject("http://service-a/hi?id="+id, String.class);
    restTemplate.exchange(new URI("http://service-a/hi?id="+id),String.class);

b. 启动类添加
@EnableDiscoveryClient
@EnableFeignClients

    	新建接口类调用
    	@Component
    	@FeignClient(value = "service-a") //这里的name对应调用服务的spring.applicatoin.name
    	public interface ServiceAFeignClient{
    		//其他微服务controller调用
    			@RequestMapping(value = "/hi")
    		String hi(@RequestParam("id") String id);
    	}
    	实例化ServiceAFeignClient实现调用

## VO 和 PO

Object Relational Mapping（对象关系映射）

VO，值对象(Value Object/View Object)
PO，持久对象(Persisent Object)

1. VO 是用 new 关键字创建，由 GC 回收的。
   PO 则是向数据库中添加新数据时创建，删除数据库中数据时削除的。并且它只能存活在一个数据库连接中，断开连接即被销毁。

2. VO 是值对象，精确点讲它是业务对象，用于业务层之间的数据传递，存在于业务逻辑层，供业务逻辑使用，存在的目的就是为数据提供一个生存的地方。
   PO 则是有状态的，每个属性代表其当前的状态。它是物理数据的对象表示，只能用在数据层。使用它，可以使我们的程序与物理数据解耦，并且可以简化对象数据与物理数据之间的转换。

3. VO 是抽象出的业务对象，它的属性是根据当前业务的不同而不同的。也就是说，它的每一个属性都一一对应当前业务逻辑所需要的数据的名称，可以和表对应，也可以不，这根据业务的需要.
   PO 的属性是跟数据库表的字段一一对应的。

**PO 对象需要实现序列化接口** implements Serializable

对于页面上要展示的对象，可以封装一个 VO 对象，将所需数据封装进去。

## DTO(Data Transfer Object)

数据传输对象。主要用于远程调用等需要大量传输对象的地方。

比如一张表有 100 个字段，那么对应的 PO 就有 100 个属性。 但是界面上只要显示 10 个字段， 客户端用 WEB service 来获取数据，没有必要把整个 PO 对象传递到客户端，

这时就可以用只有这 10 个属性的 DTO 来传递结果到客户端，这样也不会暴露服务端表结构 . 到达客户端以后，如果用这个对象来对应界面显示，那此时它的身份就转为 VO。

对于绝大部分的应用场景来说，DTO 和 VO 的属性值基本是一致的这是实现层面的思维，对于设计层面来说，概念上还是应该存在 VO 和 DTO，因为两者有着本质的区别，**DTO 代表服务层需要接收的数据和返回的数据，而 VO 代表展示层需要显示的数据**。

VO 对象通常是只读的，不允许修改。(controler 的返回值)
PO 对象通常是可读可写的。(实体类)
DTO 对象通常是只读的，不允许修改。(controler 的参数)

## dubbo 和 openFeign

OpenFeign 是 Spring Cloud 中专职于实现远程调用的一个组件，顺带整合了 Ribbon 负载均衡的功能，其底层还是基于 http 作为网络请求协议，使用较为简单。Dubbo 是一个高性能的 RPC 框架，并且具有服务注册、服务治理等功能，底层是基于 netty 使用自定义的 tcp 协议来实现的。如果对性能要求不是很高且跨主机服务调用请求量没达到服务器网络 IO 瓶颈的话，选择 OpenFeign 也是个不错的选择，毕竟整合简单、学习成本也较低。若是有大并发大请求量的远程过程调用的话，选择 Dubbo 会在性能上更为友好些，但是学习成本也会相应高。总体而言还是要针对具体的业务场景来进行分析，没有绝对的孰优孰劣。

### 微服务 dubbo

@Reference 的使用

@Reference 注入的是分布式中的远程服务对象，@Resource 和@Autowired 注入的是本地 spring 容器中的对象。

## zookeeper nacos 和 apollo 做配置中心的对比

## eureka 和 nacos

## 服务治理

服务发现
负载均衡
配置管理

## 服务保护

分布式事务
