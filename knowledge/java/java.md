---
title: java笔记
date: 2017-08-21 11:23:51
categories: java #文章分类
tags: [java] #文章标签，可以一次添加多个标签
---

java...

<!-- more -->

## 运行 shell

```shell
top pid # 能够实时显示系统中各个进程的资源占用状况，类似于Windows的任务管理器

ll /proc/ # 会列出/proc/目录下的所有子目录和文件 /proc是一个虚拟文件系统，因此其中的大多数文件和目录都是动态生成的，并且在不同的系统上可能会有所不同。

java -jar -Xms256M -Xmx256M amq-console-1.0.0.0.jar
java -jar -Dspring.config.location=config/application.yml ats-console-1.0.0.0.jar
nohup java -jar **.jar & # nohup表示终端关闭进程不受影响,&符号用于将进程放入后台运行，这样您可以继续使用终端会话而不会受到进程的干扰。

nohup java -jar *.jar & > hello.log &
more hello.log
ps -ef | grep java

kill -9 pid

tail -f filename # 会把 filename 文件里的最尾部的内容显示在屏幕上，并且不断刷新，只要 filename 更新就可以看到最新的文件内容。
tail -f /proc/<PID>/fd/1 # 标准输出（stdout）
tail -f /proc/<PID>/fd/2 # 标准错误输出（stderr

tail -n 50 文件名 # 显示最后50行

ps -aux | grep java
ps -ef | grep java

ps -aux|grep nginx
ps -aux|grep tomcat
ps -ef | grep nginx

java -jar springboot.jar --spring.profiles.active=dev
```

## 数据类型

### String & StringBuffer & StringBuilder

String 类是不可变类，任何对 String 类的改变都相当于生成了一个新的 String 对象，所以 String 类的对象是不可变的。

StringBuffer 类和 StringBuilder 类的对象都是可变类，任何对它所引用的字符串对象的改变都不会产生一个新的对象。

StringBuffer 类和 StringBuilder 类的对象都是可变类，任何对它所引用的字符串对象的改变都不会产生一个新的对象。

StringBuilder 类和 StringBuffer 类的区别在于：StringBuilder 类的方法不是线程安全的（不能同步访问），而 StringBuffer 类的方法是线程安全的（能同步访问）。

### LinkedHashMap

1. HashMap：元素按照键是无序，不重复，无索引，值不做要求

2. LinkedHashMap：元素按照键是有序，不重复，无索引，值不做要求，有序的原因是底层维护了一个双向链表

```
Map集合的体系：
        Map<K , V>(接口,Map集合的祖宗类)
       /                      \
      TreeMap<K , V>           HashMap<K , V>(实现类,经典的，用的最多)
                                 \
                                  LinkedHashMap<K, V>(实现类)
```

### LinkedHashSet

1. HashSet 集合相当于是 HashMap 集合的键，不带值

2. LinkedHashSet 集合相当于是 LinkedHashMap 集合的键，不带值

### Collections

## 引用类型和值类型

## thread

    线程5大状态
    	new
    	running
    	runnable
    	dead
    	Blocked

## callback

同步回调
代码运行到某一个位置的时候，如果遇到了需要回调的代码，会在这里等待，等待回调结果返回后再继续执行。

异步回调
代码执行到需要回调的代码的时候，并不会停下来，而是继续执行，当然可能过一会回调的结果会返回回来。

## listener

实现异步监听

## io 与 nio

## record

Java“单继承，多实现”

java 对象的组成
对象头+实例数据（对象真正存储的数据区，各种类型的字段内容）+对齐填充（占位）
对象头 = 对象自身运行时的数据 + 类型指针（指向此对象的类元数据的指针）

final 修饰的类不允许被继承

final 修饰的方法表示此方法提供的功能已经满足当前要求，不需要进行扩展，并且也不允许任何从此类继承的类来重写此方法

当 final 修饰参数时，表示此参数在整个方法内不允许被修改

### guava

google 开源的 java 工具库

## Apache 工具库

commons-collections
commons-io
commons-text
commons-lang
commons-lang3

## 类加载器

类加载器是 Java 中的一个重要概念，用于将类加载到 JVM 中以便于程序运行。每个类都由特定的类加载器加载，通常情况下，一个类只会被加载一次。

BootStrap ClassLoader
Extension ClassLoader
Application ClassLoader
Custom ClassLoader

## 值传递和对象传递

1. 值传递

```java
public void testSwap() {
   int a = 1;
   int b = 2;
   swap(a, b); // a、b值不会变化。
}
public void swap(int a, int b) {
   int c = a;
   a = b;
   b = c;
}

```

2. 对象传递

```java
public void testSwap() {
   Integer a = 1;
   Integer b = 2;
   swap(a, b); // a、b的值会被修改
   System.out.println("a = " + a + ", b = " + b);
}

public void swap(Integer a, Integer b) {
   Integer c = a;
   a = b;
   b = c;
}

```

## 可变参数

```java
// 允许在方法中定义可变数量的参数。可变参数必须是方法的最后一个参数。
public void printNumbers(int... numbers) {
    for (int number : numbers) {
        System.out.println(number);
    }
}

printNumbers(1, 2, 3, 4, 5);
```

## try-with-resources 语句

```java
// 自动关闭资源
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    System.out.println(br.readLine());
} catch (IOException e) {
    e.printStackTrace();
}
```

## 泛型

泛型只能是包装类型

### 泛型擦除

## 多线程

volatile 关键字

asynchronized 关键字

synchronized 关键字

synchronized 关键字可以用于方法和代码块。当 synchronized 关键字用于方法时，它会将整个方法标记为同步，即同一时间只有一个线程可以执行该方法。当 synchronized 关键字用于代码块时，它会将指定的代码块标记为同步，即同一时间只有一个线程可以执行该代码块。

java 异步

FutureCallback

在多线程环境下，使用 HashMap 进行 put 操作时存在丢失数据的情况，为了避免这种 bug 的隐患，强烈建议使用 ConcurrentHashMap 代替 HashMap。

golang 为啥高并发
协程更轻量，占用内存更小，这是它能做到高并发的前提。

## java8

- Optional 类

Optional.map() 方法用于将一个 Optional 对象中的值转换为另一个值，并返回一个新的 Optional 对象。如果原始 Optional 对象为空，则返回一个空的 Optional 对象。

## java11

Map.of()
List.of()

## java17

record 是 Java 14 引入的一个预览特性，后来在 Java 16 成为正式特性。Record 类是一种特殊类型的类，旨在简化不可变数据类（Immutable Data Classes）的创建过程。使用 record 关键字声明的类会自动生成构造器、getter 方法、equals()、hashCode() 和 toString() 方法，极大地减少了样板代码的编写。

```java
public record Person(String name, int age) {}
```

## 函数式编程

lambda 表达式 :

函数对象:

```java
@FunctionalInterface
public interface MyFunction<T, R> {
  R apply(T t);
}
```

常见函数接口:
Predicate

- Predicate<T> 接收一个参数并返回一个布尔值

* test(T t)

Consumer

- Consumer<T> 接收一个参数，不返回任何结果
- BiConsumer<T> 接收两个参数，不返回任何结果

Function

- Function<T, R> 接收一个参数 T 并返回一个结果 R

Supplier

- Supplier<T> 无参数，返回一个 T

```java
@FunctionalInterface
public interface Supplier<T> {
    T get()
}
```

Operator

- UnaryOperator<T> 接收单个同类型参数并返回同类型结果的操作
- BinaryOperator<T> 接收两个同类型参数并返回同类型结果的操作

方法引用:

构造方法引用:(类名::new)
Student::new 等价于()->new Student()
静态方法引用:(类名::静态方法)
Math::max 等价于(a,b)->Math.max(a,b)
System.out::println 等价于(x)->System.out.println(x)
非静态方法引用:(类名::非静态方法)
String::length 等价于(x)->x.length()
Student::getName 等价于(x)->x.getName()
Student::setName 等价于(x,y)->x.setName(y)

this::非静态方法
super::非静态方法

## Stream API

- forEach
- filter
- map
- flatMap
- reduce
- collect

### 构建

- Stream.of(T... values) 对象构建
- Arrays.stream(T[] values) 数组构建
- 集合.stream() 集合构建

### stream 的合并和截取

- Stream.concat(Stream a, Stream b) 合并两个流
- skip(long n) 跳过前 n 个元素
- limit(long n) 截取前 n 个元素
- takeWhile(Predicate p) 截取第一个不满足 p 的元素之前的元素
- dropWhile(Predicate p) 截取第一个满足 p 的元素之后的元素

### stream 的生成

- IntStream.range(1, 10)
- IntStream.rangeClosed(1, 10)
- IntStream.generate(() -> new Random().nextInt(100))
- IntStream.iterate(1, (x) -> x + 1)

### stream 的查找和判断

- filter( Predicate p).findFirst() 返回第一个元素
- filter( Predicate p).findAny() 返回任意一个元素
- anyMatch(Predicate p) 是否存在元素满足 p
- allMatch(Predicate p) 是否所有元素满足 p
- noneMatch(Predicate p) 是否所有元素都不满足 p

### stream 的去重和排序

- sorted(Comparator c) 排序
- distinct() 去重

### stream 的化简

- reduce(BinaryOperator op) 化简

### stream 的收集

- collect(Collector c) 收集

### stream 的收集器

- Collectors.toList()
- Collectors.toSet()
- Collectors.toMap(keyMapper, valueMapper)
- Collectors.joining(delimiter)

### stream 的下游收集器 groupingBy

- mapping(Function mapper, Collector downstream)
- filtering(Predicate predicate, Collector downstream)
- flatMapping(Function mapper, Collector downstream)
- counting()
- minBy(Comparator c)
- maxBy(Comparator c)
- summingInt(ToIntFunction mapper)
- averagingInt(ToIntFunction mapper)
- reducing(BinaryOperator op)

### stream 基本流

- IntStream
- LongStream
- DoubleStream

### stream 的并行流

- paraller
