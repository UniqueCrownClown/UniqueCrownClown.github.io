---
title: maven笔记
date: 2017-08-21 11:23:51
categories: java #文章分类
tags: [maven] #文章标签，可以一次添加多个标签
---

maven...

<!-- more -->

## 构建工具

### ant

    Ant 是通过 build.xml 执行构建任务的

### maven

    Maven 是一个项目管理和综合工具

    Maven 简化和标准化项目建设过程。处理编译，分配，文档，团队协作和其他任务的无缝连接。 Maven 增加可重用性并负责建立相关的任务。

    maven 本地资源库
    用来存储所有项目的依赖关系(插件 jar 和其他文件，这些文件被 Maven 下载)到本地文件夹
    当你建立一个 Maven 项目，所有相关文件将被存储在你的 Maven 本地仓库。
    Maven 的本地资源库默认为 .m2 目录文件夹
    maven 中央存储库
    构建 maven 项目,检查 pom.xml,从本地资源库获得 Maven 的本地资源库依赖资源,没有找到，从默认的 Maven 中央存储库 – http://repo1.maven.org/maven2/ 查找下载。
    添加其他远程存储库
    在 Maven 中，当你声明的库不存在于本地存储库中，也没有不存在于 Maven 中心储存库，该过程将停止并将错误消息输出到 Maven 控制台。
    例子：普通的依赖库(pom.xml)

    ```xml
    <dependency>
    <groupId>org.jvnet.localizer</groupId>
    <artifactId>localizer</artifactId>
    <version>1.8</version>
    </dependency>
    声明远程仓库在 pom.xml 文件
    <repositories>
    <repository>
    <id>java.net</id>
    <url>https://maven.java.net/content/repositories/public/</url>
    </repository>
    </repositories>
    ```

1. modelVersion  这个元素指定了 POM 的版本，Maven2 或者 Maven3 都只能是 4.0.0
2. groupId 是项目组的 ID，一般是 com.公司组织名.项目名
3. artifactId 是该项目在项目组中的 ID,比如当前的项目是项目组的一个代理项目，就可以叫做 myproxy
4. version 是项目的版本号，用于维护项目的升级和发布
5. name 一般没有实际的用处，只是用于标识该项目
6. 定制库到 Maven 本地资源库

例子：下载 “kaptcha”，将其解压缩并将 kaptcha-version.jar 复制到其他地方，比如：C 盘。

```sh
        mvn install:install-file -Dfile=c:\kaptcha-{version}.jar -DgroupId=com.google.code -DartifactId=kaptcha -Dversion={version} -Dpackaging=jar
```

        安装完毕后，就在 pom.xml 中声明 kaptcha 的坐标。

```xml
         <dependency>
        	  <groupId>com.google.code</groupId>
        	  <artifactId>kaptcha</artifactId>
        	  <version>2.3</version>
         </dependency>
```

```sh
mvn clean package -Dmaven.test.skip= true
```

### gradle

gradle 的使用说明

import project = import build.gradle = import pom.xml

## maven

mvn install 把 jar 安装到本地仓库

## 分模块开发

## 依赖管理

### 依赖传递

依赖具有传递性

依赖传递的冲突问题：在依赖传递过程中产生了冲突，有三种优先法则

- 路径优先：当依赖中出现相同资源时，层级越深，优先级越低，反之则越高

- 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖靠后的

- 特殊优先：当同级配置了相同资源的不同版本时，后配置的覆盖先配置的

### 可选依赖

可选依赖是现在用的东西不想给别人用的时候，使用可选依赖。
不透明

<optional>true</optional>用于指定一个依赖是否是可选的。
当一个依赖被标记为 <optional>true</optional>，意味着这个依赖不会自动传递到依赖该项目的其他项目中。
换句话说，当项目 A 依赖项目 B，而项目 B 声明了一个带有 <optional>true</optional> 的依赖 C，则项目 A 不会继承依赖 C，除非项目 A 显式地添加了对依赖 C 的引用。

### 排除依赖

排除依赖是不想用别人的东西的时候，使用排除依赖。
排除依赖不需要指定版本
不需要

```xml
<dependency>
    <groupId>com.itheima</groupId>
    <artifactId>maven_04_dao</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!--排除依赖是隐藏当前资源对应的依赖关系-->
    <exclusions>
        <exclusion>
            <groupId>com.itheima</groupId>
            <artifactId>maven_03_pojo</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## 聚合

会自动分析模块的依赖关系，先构建依赖关系，然后按照依赖关系顺序构建项目，依赖关系先构建不被依赖的项目

```xml
<!-- 父工程配置 -->
<packaging>pom</packaging>
<modules>
    <module>maven_01_pojo</module>
</modules>
```

```xml
<!-- 子工程配置 -->
<parent>
    <groupId>com.itheima</groupId>
    <artifactId>maven_01_pojo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <relativePath>../maven_01_parent/pom.xml</relativePath>
</parent>
```

## 继承

### 通用依赖

```xml
<dependences>
<dependency>
<groupId>com.itheima</groupId>
<artifactId>maven_01_pojo</artifactId>
<artifactId>maven_01_pojo</artifactId>
<version>1.0-SNAPSHOT</version>
<scope>provided</scope>
</dependency>
</dependences>
```

### 不通用依赖限制版本

```xml
<dependencyManagement>
<dependences>
<dependency>
<groupId>com.itheima</groupId>
<artifactId>maven_01_pojo</artifactId>
<artifactId>maven_01_pojo</artifactId>
<version>1.0-SNAPSHOT</version>
<scope>provided</scope>
</dependency>
</dependences>
</dependencyManagement>
```

dependencyManagement 里只是声明依赖，并不实现引入，因此子项目需要显式地声明需要的依赖。如果不在子项目中声明依赖，是不会从父项目中继承的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且 version 和 scope 都读取自父 pom; 如果子项目中指定了版本号，那么会使用子项目中指定的 jar 版本。
dependencyManagement 让子项目引用依赖，而不用显示的列出版本号。Maven 会沿着父子层次向上走，直到找到一个拥有 dependencyManagement 元素的项目，然后它就会使用在这个 dependencyManagement 元素中指定的版本号,实现所有子项目使用的依赖项为同一版本。

## 属性

```xml
<properties>
<spring.version>5.0.6</spring.version>
</properties>
```

## 配置文件加载属性

可以在配置文件中用在 pom 中定义的 properties

## 其他属性

- 自定义属性
- 内置属性
- 系统属性
- 环境变量

## 依赖范围

scope 标签的取值有四种：compile,test,provided,runtime

compile: 默认范围，在项目构建过程中需要用到的依赖（src/main 目录范围内有效）
test: 在测试过程中用到的依赖（src/test 目录范围内有效）
provided: 不参与打包
runtime: 不参与打包

## 版本管理

- 工程版本

1. SNAPSHOT
2. RELEASE

- 发布版本

1. alpha 版
2. beta 版
3. 纯数字版本

## nexus 私服

1. 宿主仓库 hosted 自主研发，三方资源 上传
2. 代理仓库 proxy 代理连接中央仓库 下载
3. 仓库组 group 为仓库编组简化下载操作 下载

nexus 新建 hosted 仓库 maven-release 和 maven-snapshot
添加到仓库组 maven-public 下管理

maven 配置 server 和 mirror

在项目中配置
distributionManagement->repository->url->maven-release
distributionManagement->snapshotRepository->url->maven-snapshot

maven deploy 到私服
