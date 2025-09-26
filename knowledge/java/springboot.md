---
title: springboot
date: 2019-08-21 11:23:51
categories: java #文章分类
tags: [springboot] #文章标签，可以一次添加多个标签
---

spring boot...

<!-- more -->
## 依赖顺序

spring-boot-starter -> spring-boot-autoconfigure
spring-boot-starter-parent->spring-boot-dependencies

## 为什么spring boot打包的jar可以直接运行？

## springboot 的启动过程

## springboot 的 spi 机制(Service Provider Interface)

拓展点机制

spring 框架里面一定是有一个类似于 ServiceLoader 的类, 专门从 META-INF/spring.factories 里面的配置,加载特定接口的实现.

## spring-boot-starter 和 module

spring-boot-starter 是一种机制，用于快速启动 Spring Boot 应用程序，并提供自动配置和依赖项管理。

Module 是一种机制，用于组织和管理应用程序的代码。它通常是一个独立的代码单元，可以被其他模块所依赖。

封装代码到三方包，并提供宿主包的依赖设置注入

## 自动配置机制

resources/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports 文件是 Spring Boot 自动配置机制的一部分，用于指定哪些自动配置类应该被 Spring 容器扫描和激活。这个文件是 Spring Boot 2.7.x 版本及更高版本中用来替代旧的 META-INF/spring.factories 文件的一种方式，尽管后者在一些场景下仍然被支持。
在 Spring Boot 中，自动配置是通过一系列的自动配置类实现的，这些类通常以 AutoConfiguration 结尾。每个自动配置类负责配置特定的框架或技术栈，例如数据库连接、Web MVC、安全框架等。当 Spring Boot 启动时，它会查找并加载这些自动配置类，根据应用的依赖和配置自动设置相应的Bean。
org.springframework.boot.autoconfigure.AutoConfiguration.imports 文件的格式通常是一系列的类名，每一行指定一个自动配置类。例如：
```
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration

```
当 Spring Boot 的自动配置机制运行时，它会读取这个文件，并基于其中列出的类名来决定哪些自动配置应该被激活。如果应用的类路径中存在这些自动配置类所依赖的库，那么相应的自动配置就会生效。
这种机制简化了 Spring Boot 应用的开发，因为它允许开发者专注于业务逻辑，而不需要显式地配置大量的框架组件。Spring Boot 能够智能地检测应用的依赖，并自动应用合适的配置，从而减少了配置工作量，提高了开发效率。

## 自动装配扫描
```java
//spring.facories
org.springframework.boot.autoconfigure.EnableAutoConfiguration=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```
