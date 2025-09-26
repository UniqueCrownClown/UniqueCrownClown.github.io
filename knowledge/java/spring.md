---
title: spring
date: 2019-08-21 11:23:51
categories: java #文章分类
tags: [spring] #文章标签，可以一次添加多个标签
---

spring...

<!-- more -->

## IOC

### 使用 Ioc 容器管理 bean(Ioc)

在 Ioc 容器中将有依赖关系的 bean 进行关系绑定(DI)

### 获取容器

BeanFactory
ApplicationContext
ConfigurableApplicationContext
ClassPathXmlAplicationContext
FileSystemXmlApplicationContext
参考子类继承的设计思想

```

```

### bean 的实例化

1. 构造方法实例化 bean

```xml
<bean id="myBean" class="com.example.MyClass"/>
```

2. 静态工厂实例化 bean

```xml
<bean id="myBean" class="com.example.Myfactory" factory-method="getMyBean"/>
```

3. 实例工厂实例化 bean

```xml
<bean id="myFactory" class="com.example.MyFactory"/>
<bean id="myBean" factory-bean="myFactory" factory-method="getMyBean"/>
```

4. 使用 factorybean 实例化

工厂类实现了 FactoryBean 接口

```java
class MybeanFactory  implements FactoryBean<MyBean>{
    @Override
    public MyBean getObject(){
        return new MyBean();
    }
    @Override
    public class<?> getObjectType(){
        return MyBean.class;
    }
    @Override	//是否单例
    public boolean isSingleton() {
        return false;
    }
}
```

```xml
<bean id="myBean" class="com.example.MybeanFactory" init-method="init" destroy-method="destroy"/>

```

### bean 的生命周期

init-method
destory-method
或者实现 InitializingBean, DisposableBean

### 自动装配

自动装配优先级低于 setter 注入和构造器注入，同时出现自动装配配置失效

### 三方 bean 的管理

三方 bean 的依赖注入

bean 声明中使用 properties

## DI

依赖注入方式

1. 构造器注入

- 依赖项通过构造函数参数传递对象
- 这是最推荐的依赖注入方式，因为它强制要求依赖项在对象创建时就存在，有助于提高对象的不可变性和可测试性。

```java
     public class MyClass {
         private final MyDependency dependency;

         @Autowired
         public MyClass(MyDependency dependency) {
             this.dependency = dependency;
         }
     }
```

2. 字段注入

- 依赖项直接注入到类的字段中。
- 这种方式简单直接，但可能会降低代码的可测试性，因为依赖项不是通过构造函数或方法显式提供的。

```java
     public class MyClass {
         @Autowired
         private MyDependency dependency;
     }
```

3. setter 方法注入

- 依赖项通过 setter 方法注入。
- 这种方式允许在运行时更改依赖项，但可能破坏对象的封装性。

```java
     public class MyClass {
         private MyDependency dependency;

         @Autowired
         public void setDependency(MyDependency dependency) {
             this.dependency = dependency;
         }
     }
```

## xml 开发

```xml
<bean
    id="myBean" // bean的id
    name="myBean" // bean的别名
    class="com.example.MyClass" // bean类型,静态工厂类,FactoryBean
    scope="singleton" // bean的实例数量
    init-method="init" // 初始化方法
    destroy-method="destroy"// 销毁方法
    autowired="true" // 是否自动注入依赖项
    lazy-init="true" // 是否延迟初始化
    factory-bean="myFactoryBean" // 使用工厂bean创建bean
    factory-method="getMyBean" // 使用工厂方法创建bean
/>
```

## 注解开发
- Spring Boot自动装配，Spring Boot 项目借助 @SpringBootApplication 注解开启自动配置，该注解包含了 @ComponentScan 功能。@SpringBootApplication 组合了 @Configuration、@EnableAutoConfiguration 和 @ComponentScan 这三个注解。

@SpringBootApplication 注解**会自动扫描与 YourApplication 类同包及其子包下的组件，并将其注册为 Bean**。

- @Component @Controller @Service @Repository

通过使用 @Component 注解，Spring 可以自动扫描和识别被标记的类，并将其实例化为 Bean 并添加到应用程序的上下文中。这样，我们就可以在应用程序中使用 @Autowired 或 @Resource 注解来自动装配这些 Bean。

除了 @Component 注解外，Spring 还提供了其他几个注解，如 @Service、@Repository 和 @Controller，它们都是基于 @Component 注解实现的，并且都具有相同的作用，只是在语义上有所区别。例如，@Service 注解用于标记业务逻辑层的组件，而 @Repository 注解用于标记数据访问层的组件。

@RestController = @Controller + @ResponseBody

Java 类代替 Spring 核心配置文件@Configuration+@ComponentScan(多个使用数组格式)-->AnnotationConfigApplicationContext 加载配置类,从上下文中获取装配的 bean 实例

```java
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
User user = ctx.getBean(User.class);
system.out.println(user);
```

- bean 作用范围

@Scope("prototype")
@Scope("singleton")

- bean 周期

1. @PostStruct
   这个注解是由 Java EE 规范定义的，用于标记在依赖注入完成后执行初始化方法。
   标记了 @PostConstruct 的方法会在所有依赖注入完成后，但在任何其他初始化方法（如 init-method 属性指定的方法）之前调用。
   该方法不能接受任何参数，并且返回类型必须是 void。

2. @PreDestroy

同样由 Java EE 规范定义，用于标记在 Bean 销毁前执行的方法。
当容器决定销毁一个 Bean 时，会先调用标记了 @PreDestroy 的方法。
和 @PostConstruct 一样，该方法不能接受任何参数，并且返回类型必须是 void。

- 自动装配

@Autowired 类型匹配,实现引用类型的注入
@Autowired+@Qualifier(beanName) 匹配

@Value 实现简单类型的注入
@PropertySource("classpath:jdbc.properties")(多个使用数组格式)

- 三方 bean 管理

@Import
@Bean 返回一个要注入的

- 三方 bean 依赖注入

简单类型用@Value+成员变量
引用类型直接用形参，只要能扫到就能自动装配注入

## AOP

一种无侵入式的功能增强的编程范式,对应 oop,面对对象编程

- 连接点(JoinPoint)程序执行的任意位置，粒度为执行方法，设置变量，抛出异常等
- 切入点(Pointcut)哪些方法需要追加功能的，匹配通知的方法，叫切入点
- 通知(Advice)各个方法共用的功能(@Before,@After, @AfterReturning, @AfterThrowing, @Around)
- 通知类 定义通知的类
- 切面(Aspect)切面描述的是通知共用的功能与所对应切入点的关系。在哪些切入点上执行哪些通知叫切面

@EnableAspectJAutoProxy

@Component
@Aspect

@Pointcut(execution(\* com.atguigu.spring.service.impl.AccountServiceImpl.transfer(..)))

工作流程
AOP 的本质是代理模式

切入点表达式

@Order(1) 用于指定切面执行顺序的注解。在存在多个切面的场景下，@Order 注解能设定切面的优先级，数值越小优先级越高。

@Order(1)  // 确保在事务之前执行

通知类型
@Before 形参 JoinPoint 在目标方法执行之前执行
```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {
    @Before("execution(* com.example.service.*.*(..))")
    public void beforeAdvice(JoinPoint joinPoint) {
        System.out.println("Before method: " + joinPoint.getSignature().getName());
    }
}
```
@After 形参 JoinPoint 无论目标方法正常返回还是抛出异常，都会在目标方法执行结束后执行。
@Around 环绕通知(重点) 是功能最强大的通知类型，能在目标方法执行前后进行自定义操作，还能控制目标方法是否执行。 形参 ProceedingJoinPoint.proceed() 表示对原始操作的调用
@AfterReturning 在目标方法正常返回后执行
@AfterThrowing 在目标方法抛出异常时执行

## Spring 事务

mybatis 使用的是 jdbc 事务

Spring 开启事务，使用注解版本，需要三步
1：定义的 service 接口的方法，添加@Transactional 注解
2：在 jdbcConfig.java 配置类中，设置事务管理器 DataSourseTransactionManager
3：在 SpringConfig.java 中，开启注解式事务驱动。@EnableTransactionManagement

事务角色
事务管理员
事务协调员

事务相关配置
readOnly
timeout = -1 # -1 表示不设置超时时间
rollbackFor={NullPointExecption.class} 表示遇到了 NullPointExection 异常，会开启事务。

propagation = Propagation.REQUIRED # 默认值，表示当前方法开启事务，如果当前方法没有开启事务，则开启事务，加入事务
Propagation.REQUIRES_NEW # 表示当前方法开启事务，如果当前方法没有开启事务，则开启事务,一定新开事务

## 常见 web 框架

表示层
通过 JSP 页面实现交互界面，负责传送请求(Request)和接收响应(Response)，然后 Struts 根据配置文件(struts-config.xml)将 ActionServlet 接收到的 Request 委派给相应的 Action 处理。

业务逻辑层
管理服务组件的 Spring IoC 容器负责向 Action 提供业务模型(Model)组件和该组件的协作对象数据处理(DAO)组件完成业务逻辑，并提供事务处理、缓冲池等容器组件以提升系统性能和保证数据的完整性。

数据持久层
依赖于 Hibernate 的对象化映射和数据库交互，处理 DAO 组件请求的数据，并返回处理结果。

域模块层(实体层)

struts+spring+hibernate
集成 SSH 框架的系统

Spring+SpringMVC+Mybatis

spring boot

spring cloud

## record

-Dspring.profiles.active=dev

spring 如何动态注入 bean

@ConditionalOnClass
@ConditionalOnMissingBean(TokenStore.class)
@ConditionalOnProperty("spring.redis.host")
@ConditionalOnExpression
@ConditionalOnClass(DispatcherServlet.class)

@Configuration

存在多个接口类的实现类
@Qualifier("service")
@Service("service")

spring-session-data-redis 的使用和影响

spring-data-jpa
JPA 默认使用 Hibernate 作为 ORM 实现，所以，一般使用 Spring Data JPA 即会使用 Hibernate。
hibernate 和 mybatis 比较
Hibernate 是面向对象的，而 MyBatis 是面向关系的
Hibernate 可以根据配置信息自动生成数据库表

@Resource 和@Autowired
@Autowired 是 byType 自动注入，而@Resource 默认 byName 自动注入
引入由 Spring 容器管理的 bean

ApplicationContext 实例-->beanFactory-->bean-->装配的 service 对象
BeanDefinition
Spring 默认是不同文件的时候可以 Bean 覆盖的。

经典 null 和 equals 的错误使用
把已知常量作为 equals 的调用方,避免 NPE
Object.equals(1,key)

map.put & map.putIfAbsent

@requestParam 注解,会对字符串中的特殊字符进行转义，以确保它们不会被误解为其他含义。例如，如果字符串中包含“&”或“=”，则这些字符将被转义为它们的 HTML 实体编码。

```shell
mvn install -DskipTests #忽略测试用例直接build
mvn -DskipTests=true

```

## bean 的理解 & @ComponentScan

@bean 添加在 class 上和 function 上的区别

当@bean 注解用于类上时，它表示该类将被注册为一个 bean，Spring 容器将负责创建该类的实例，并将其管理起来。这意味着你可以在其他类中使用@Autowired 等注解来自动装配该类的实例。

当@bean 注解用于函数上时，它表示该函数将返回一个对象，该对象将被注册为一个 bean。这通常用于创建那些不能通过构造函数注入的对象，例如第三方库中的对象。在这种情况下，你可以使用@Bean 注解来告诉 Spring 容器如何创建该对象，并将其注册为一个 bean。

## lombok 的构造函数注解

- @AllArgsConstructor
  会生成一个包含类中所有非静态、非 final 字段的构造函数

```java
@AllArgsConstructor
public class Person {
    private String name;
    private int age;
}
// 手动实现
public class Person {
    private String name;
    private int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

- @RequiredArgsConstructor

final 字段在 Java 里必须在对象创建时初始化，不能为 null
会生成一个包含类中所有 `final` 字段和用 `@NonNull` 注解标记的非 `final` 字段的构造函数。

```java
@RequiredArgsConstructor
public class Person {
    private final String name;
    private int age;
}
// 手动实现
public class Person {
    private String name;
    public Person(String name) {
        this.name = name;
    }
}
```

- @NoArgsConstructor
  会生成一个无参数的构造函数

```java
@NoArgsConstructor
public class Person {
    private String name;
    private int age;
}
// 手动实现
public class Person {
    private String name;
    private int age;
    public Person() {
    }
}
```

- @Data

这是一个组合注解，它相当于同时使用了 @ToString, @EqualsAndHashCode, @Getter on all non-final fields, @Setter on all non-final fields, and @RequiredArgsConstructor。通常用于实体类或者 DTOs。

## Date 对象映射

