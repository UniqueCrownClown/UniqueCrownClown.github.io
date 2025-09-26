---
title: springmvc
date: 2019-08-21 11:23:51
categories: java #文章分类
tags: [springmvc] #文章标签，可以一次添加多个标签
---

springmvc...

<!-- more -->

## 构建流程

```xml
<dependencies>
<dependency>
<groupId>javax.servlet</groupId>
<artifactId>javax.servlet-api</artifactId>
<version>3.1.0</version>
</dependency>
<dependency>
<groupId>org.springframework</groupId>
<artifactId>spring-webmvc</artifactId>
<version>5.1.5.RELEASE</version>
</dependency>
</dependencies>
<build>
<plugins>
<plugin>
<groupId>org.apache.tomcat.maven</groupId>
<artifactId>tomcat7-maven-plugin</artifactId>
<version>2.1</version>
<configuration>
<port>8080</port>
<path>/</path>
</configuration>
</plugin>
</plugins>
</build>
```

servlet 容器启动的配置类，在里面加载 spring 的配置

```java
@Configuration
@ComponentScan("com.atguigu.controller")
public class SpringMvcConfig{

}

public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {

@Override
protected WebApplicationContext createServletApplicationContext() {
    AnnotatedConfigWebApplicationContext ctx = new AnnotatedConfigWebApplicationContext();
    ctx.register(SpringMvcConfig.class);
    return ctx;
}

@Override
protected String[] getServletMappings() {
    return new String[]{"/"};
}

@Override
protected WebApplicationContext createRootApplicationContext() {
    return null;
}
}
```

## 拦截器

Filter 属于 Servlet 技术
Interceptor 属于 SpringMVC 技术

Filter 对所有的访问增强
Interceptor 只对 springmvc 的访问增强

1：定义一个类 implements HandlerInterceptor，重写三个 default 的方法
2：写配置类，extends WebMvcConfigurationSupport

拦截器的定义，实现了 HandlerInterceptor 的接口，覆盖三个方法
如何覆盖定义了的拦截器的类，需要 addInterceptors 的方法

拦截器链：多个拦截器，拦截器执行顺序是按照定义的顺序执行(addInterceptors)，
