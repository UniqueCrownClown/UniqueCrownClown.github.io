---
title: spring record
date: 2017-08-21 11:23:51
categories: java #文章分类
tags: [spring record] #文章标签，可以一次添加多个标签
---

spring record...

<!-- more -->

## springboot 日志

springboot 会自动配置 SLF4J 和 logback,不需要在项目的 pom.xml 文件中显式声明这些依赖项
@Slf4j
自动实例化 log,可以直接用 log.info/error/warn

Spring Boot 使用 SLF4J 作为其日志抽象层，而 Logback 是 SLF4J 的一个实现。

如果你需要自定义 Logback 的配置，可以通过在项目的 classpath 根目录下放置一个名为 logback.xml 或者 logback-spring.xml 的配置文件来实现。Spring Boot 推荐使用 logback-spring.xml，因为这个文件名会自动引入一些 Spring Boot 的扩展功能，例如环境特定的配置。

此外，你还可以在 application.properties 或 application.yml 文件中进行一些基本的日志配置，但对于更复杂的日志处理，通常推荐使用 XML 配置文件。

## record

@Autowired 注解可以与 @Qualifier 注解一起使用，用于指定具体的 Bean。

@Resource 注解可以与 @Qualifier 注解一起使用，用于指定具体的 Bean

@Bean 注解在 Spring 框架中用于将一个 **Java 方法**返回的对象转换为一个 Bean，并将其添加到 Spring 容器中。

当我们使用 @Bean 注解时，Spring 会默认使用方法名作为 Bean 的名称，并根据方法返回值的类型确定 Bean 的类型。例如，如果我们有一个 getUserService() 方法返回一个 UserService 类的实例，那么 Spring 将会自动将该实例注册为一个类型为 UserService 的 Bean，并使用 "getUserService" 作为 Bean 的名称。

当然，我们也可以通过在 @Bean 注解中指定 name 或 type 属性来自定义 Bean 的名称或类型。

@Primary 注解是 Spring 框架中的一个特性,主要用于指定某个 Bean 作为某一类型的默认实现。当 Spring 容器中存在多个相同类型的 Bean 时,使用@Primary 可以告诉 Spring 容器,优先选用被标注为@Primary 的 Bean。

@Builder

builder()的实现
Builder 模式是一种创建对象的设计模式，它允许你逐步构造一个复杂的对象。这种模式特别适用于对象需要很多参数的情况，其中一些参数可能是可选的。使用 Builder 模式可以避免构造函数中参数过多的问题，并且可以使得代码更加清晰和易于维护。 


自定义注解

```java
@Target({ ElementType.PARAMETER, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
```
配置加载三种方式

1. @Value("${ruoyi.name}")
2. @Autowired + Environment env + env.getProperty("ruoyi.name")
3. 实体类封装对象+@ConfigurationProperties(prefix = "ruoyi")+@Component+@Autowired

@InitBinder

@PostConstruct
用于指定一个方法在该类的依赖注入完成后立即执行。通常用于初始化对象状态或执行必要的启动任务。

开启事务
@EnableTransactionManagement
@Transactional

Lokmok 库
@Data

成员变量的优雅注入
@RequireArgsConstructor
@NoArgsConstructor
@AllArgsConstructor

Empty&&Blank

Springboot + mybatis 配置多数据源



mybatis 推到 mq
定义拦截器：使用 MyBatis 提供的插件机制，编写一个拦截器，在执行数据库操作之前将其转换为对应的消息，并发送到 MQ 中。

mysql 同步 es

同步双写
异步双写
增加一个定时器程序，让该程序按一定的时间周期扫描指定的表，把该时间段内发生变化的数据提取出来；逐条写入到 ES 中。

基于 Binlog 的实时同步
缓存和数据库一致性的方案，使用 Canal 监听 Binlog 模式
该方案将数据库的数据变更通过 Canal 转发给消息队列的特定 Topic。客户端应用程序可以监听该消息队列的 Topic，以保持缓存与数据库的一致性。

Spring Task

@EnableScheduling 和 @Scheduled

@EnableAspectJAutoProxy(exposeProxy = true)

spring cache

```
@EnableCaching
@Cacheable(cacheNames = "setmealCache",key = "#categoryId") # 查询的时候缓存
@CacheEvict(cacheNames = "setmealCache",key = "#setmealDTO.categoryId") # 新增修改删除的时候清除
```

@Cacheable 注解是 Spring 缓存抽象的一部分,它用于标注方法,指示该方法的返回值应该被缓存。当再次调用该方法时,如果缓存中存在相应的值,则直接从缓存中获取,而不需要重新执行方法。这样可以提高应用程序的性能。

@CacheEvict 注解则用于标注需要清除缓存的方法。当某个数据发生变化时,需要清除相关的缓存,以确保下次获取的是最新的数据。

***@RestControllerAdvice***
用于定义一个全局的异常处理器类，它可以捕捉并处理在 REST 控制器中抛出的异常。它实际上是 @ControllerAdvice 和 @ResponseBody 注解的组合体，意味着使用它的类将能够处理控制器级别的异常，并且返回的数据将直接转换为响应体的一部分，而不是视图模板。

***@ExceptionHandler***
通过 @ExceptionHandler 注解的方法可以捕获特定类型的异常，并返回一个预定义的响应或错误信息。这对于 RESTful API 特别有用，因为你可以提供一致的错误响应格式。

通常情况下，如果你使用的是 Spring Boot，那么 @RestControllerAdvice 类会自动被 Spring 的组件扫描所识别。但是，如果你在非 Spring Boot 环境中使用，可能需要确保你的配置正确包含了 @EnableWebMvc 注解，以便启用 MVC 的增强功能。
