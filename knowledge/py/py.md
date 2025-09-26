---
title: python基础
date: 2022-08-01 11:23:51
categories: python #文章分类
tags: [python] #文章标签，可以一次添加多个标签
---

python base...

<!-- more -->

## 运行环境

安装 python3

```shell
python pyfilePath #运行py文件
```

## 包管理器

### pip

- 安装第三方模块
  一般来说，第三方库都会在 Python 官方的 pypi.python.org 网站注册，要安装一个第三方库，必须先知道该库的名称，可以在官网或者 pypi 上搜索，比如 Pillow 的名称叫 Pillow，因此，安装 Pillow 的命令就是：

```bash
pip install Pillow
pip install -u # 是升级安装
pip list #查看依赖包
```

- 自动安装所有依赖项

```bash
pip install -r requirements.txt
```

- 升级

```shell
python -m pip install --upgrade pip
```

- 修改镜像源

```shell
pip install numpy -i https://pypi.douban.com/simple/
pip config set global.index-url https://pypi.douban.com/simple/
# Writing to C:\Users\Administrator\AppData\Roaming\pip\pip.ini
```

- 生成 requiesment.txt

1. 方法一：该方法会把当前环境的 所有包 都生成到 requirements.txt 文件

```shell
pip freeze > requirements.txt
```

2. 方法二：该方法只生成 当前项目所需要的 requirements.txt 文件

```shell
pip install pipreqs
pipreqs . --encoding=utf-8 --force
```

使用该方法记得把工作目录切换到项目目录下。

- Anaconda
  基于 Python 的数据处理和科学计算平台，它已经内置了许多非常有用的第三方库

- 常用三方库
  MySQL 驱动程序，Web 框架 Flask，科学计算 Numpy 等。用 pip 一个一个安装费时费力，还需要考虑兼容性。

### poetry 依赖管理和打包

poetry 是一个 Python 虚拟环境和依赖管理的工具。
poetry 和 pipenv 类似，另外还提供了打包和发布的功能。
通过 pyproject.toml 文件，
不仅可以配置依赖包，还可以用于区分开发、测试、生产环境、配置源路径

```shell
pip install poetry
pip upgrade poetry
pip uninstall poetry
```

### 环境管理

使用 Miniconda来进行 Python

```shell
conda info --envs # 显示安装环境和安装路径
conda list -n <environment_name> # 显示环境安装的所有包
conda create --name <environment_name> [package1](pathon==3.10.6)[package2] ...
conda activate <environment_name>
conda deactivate
conda remove --name <env_name> --all # 删除环境和包

```
建议每个项目使用一个独立的环境，这样可以确保每个项目所需的依赖包不会互相干扰。使用独立的环境还可以方便地管理每个项目的依赖关系，避免因为不同项目所需的依赖包版本不同而导致冲突和错误。

## 常见 web 架构

1. flask ＋ mongoDB
2. django ＋ sqlites
3. tornado
4. python bottle
5. FastAPI + uvicorn
6. sanic

## 扩展

1. AI 学习分类
   - 深度学习(tensorflow,pytorch,Paddle)
   - 增强学习
   - 迁移学习
2. google 的产品
   - Cloud AutoML
   - VR
3. ide 工具
   - vscode
     f5 --- debugger 模式

## 语法

### simpleTest

```py
# input获取命令行输入
name = input()
# 控制台输出变量
print('hello,',name)
```

### 基本规则

1. 采用缩进式
2. 以#开头的语句是注释解释器会忽略掉注释。其他每一行都是一个语句，
3. 当语句以冒号:结尾时，**缩进的语句**视为代码块。
4. 按照约定俗成的惯例，应该始终坚持使用 4 个空格的缩进。不混用 tab 和空格
5. Python 程序是大小写敏感的，如果写错了大小写，程序会报错

```py
# print absolute value of an integer:
a = 100
if a >= 10:
    print(a)
elif a >= 0:
    print(a,"是小于10的整数")
else:
    print(-a)
```

6. 循环

```py
# for in 循环
sum1 = 0
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
    sum1 = sum1 + x
print(sum1)
# while 循环
sum2 = 0
n = 99
while n > 0:
    sum2 = sum2 + n
    n = n - 2
print(sum2)
# break 和 continue
n = 0
while n < 10:
    n = n + 1
    if n % 2 == 0: # 如果n是偶数，执行continue语句
        continue # continue语句会直接继续下一轮循环，后续的print()语句不会执行
    elif n % 5 == 0:
        break
    print(n)
```

7. 作用域
   有的函数和变量我们希望给别人使用，有的函数和变量我们希望仅仅在模块内部使用。在 Python 中，是通过\_前缀来实现的。
   类似\_xxx 和\_\_xxx 这样的函数或变量就是非公开的（private），不应该被直接引用，比如\_abc，\_\_abc 等
8. 断言-assert
9. 动态语言的鸭子类型
10. hasattr
    hasattr() 函数的用法，该函数的功能是查找类的实例对象中是否包含指定名称的属性或者方法，但该函数有一个缺陷，即它无法判断该指定的名称，到底是类属性还是类方法。
    要解决这个问题，我们可以借助可调用对象的概念,用\_\_call\_\_来判断
11. 使用*args 和\*\*kwargs 的含义
    *args 是用来发送一个非键值对的可变数量的参数列表给一个函数
    \*\*kwargs 允许你将不定长度的键值对, 作为参数传递给一个函数

```py
# 非键值对参数
def func(*args):
    for i in args:
        print(i)
func(3,2,1,4,7)
# 关键字参数
def func(**kwargs):
    for i in kwargs:
        print(i,kwargs[i])
func(a=1,b=2,c=7)
```

12. 运算符

- 逻辑运算符 and,or,not
- 成员运算符 in 和 not in
- 身份运算符 is 和 is not

13. 没有三目运算符，只有三目运算子
    [on true] if [expression] else [on false]

```py
a,b=2,3
min= a if a<b else b
print(min)
# 2
```

14. 关键字

and or not
pass import try finally raise
is in as del from
True Flase None
assert print
for break continue while if elif else
def class with yield lambda
global exec nonlocal return

15. str 操作

- join(),split(),
- strip(),lstrip(),rstrip(),

```py
testString="saaaay yes no yaaaass"
print testString.strip("say") #传入字符数组,首尾除去匹配的字符
# 当没有传入参数时，默认去除首尾空格
# yes no
```

- lower(),upper(),isupper(),islower()

16. global 和 nonlocal

- global
  局部要对全局变量修改，应在局部声明该全局变量,只读则不用

```py
count = 0
def global_test():
    global count
    count += 1
    print(count)
global_test()
```

- nonlocal

nonlocal 声明的变量不是局部变量,也不是全局变量,而是外部嵌套函数内的变量。

```py
def nonlocal_test():
    count = 0
    def test2():
        nonlocal count
        count += 1
        return count
    return test2
val = nonlocal_test()
print(val()) # 1
print(val()) # 2
print(val()) # 3
```

17. is 和 ==

当变量是数字、字符串、元组，列表，字典时，is 和==都不相同， 不能互换使用！当比较值时，要使用==，比较是否是同一个内存地址时应该使用 is。当然，开发中比较值的情况比较多。

18. 三个引号

- 用于字符串跨行，不必使用拼接符或换行符，直接使用三个引号即可
- 用于注释

```py
multi-line = """
nice to meet you!
    nice to meet you!
        nice to meet you!"""
'''
@desc: 计算两个数的和
@param a: 加数a
@param b: 加数b
'''
def sum (a,b):
    return a + b
print(sum())
```

19. python 中的[1:]、[::-1]、X[:,m:n]和 X[1,:]

- [1:] 去掉列表中第一个元素（下标为 0），去后面的元素进行操作
- [::-1] 列表倒叙遍历
  这个是 python 的 slice notation 的特殊用法。
  b = a[i:j] 表示复制 a[i]到 a[j-1]，以生成新的 list 对象
  当 i 缺省时，默认为 0，即 a[:3]相当于 a[0:3]
  当 j 缺省时，默认为 len(alist), 即 a[1:]相当于 a[1:10]
  当 i,j 都缺省时，a[:]就相当于完整复制一份 a 了

b = a[i:j:s]这种格式呢，i,j 与上面的一样，但 s 表示步进，缺省为 1.
所以 a[i:j:1]相当于 a[i:j]
当 s<0 时：i 缺省时，默认为-1； j 缺省时，默认为-len(a)-1
所以 a[::-1]相当于 a[-1:-len(a)-1:-1]，也就是从最后一个元素到第一个元素复制一遍。

- X[:,m:n]和 X[1,:]
  X[:,0]是 numpy 中数组的一种写法，表示对一个二维数组，取该二维数组第一维中的所有数据，第二维中取第 0 个数据，直观来说，X[:,0]就是取所有行的第 0 个数据, X[:,1] 就是取所有行的第 1 个数据。

X[n,:]是取第 1 维中下标为 n 的元素的所有值。

X[:, m:n]，即取所有数据的第 m 到 n-1 列数据，含左不含右

```py
a = ['a','b','c','d','e','f','g','h','g','k','l','m']
b = a[:]  # 列表切片，表示把列表a[]的值全部正序复制到列表b[]中
print(b)  # ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'g', 'k', 'l', 'm']
# b = a[n:m]表示列表切片，复制列表a[n]到a[m-1]的内容到新的列表对象b[]
# 当n缺省时，默认为0，即a[:m]
# 当m缺省时，默认到最后，即a[n:]
b1 = a[1:4]
print(b1)  # ['b', 'c', 'd']
b2 = a[:3]
print(b2)  # ['a', 'b', 'c']
b3 = a[1:]
print(b3)  # ['b', 'c', 'd', 'e', 'f', 'g', 'h', 'g', 'k', 'l', 'm']
# b = a[i:j:s]这种格式呢，i,j与上面的一样，但s表示步进，缺省为1，s可以取任何数字.
# 所以a[i:j:1]相当于a[i:j]
b4 = a[1:5:2]
print(b4)  # ['b', 'd']
b5 = a[:5:-1]  # 从末尾倒数取值
print(b5)  # ['m', 'l', 'k', 'g', 'h', 'g']
b6 = a[5::-2]
print(b6)  # 从a[n]处倒数取值
b7 = a[::-1]  # 到这取值
print(b7)  # ['m', 'l', 'k', 'g', 'h', 'g', 'f', 'e', 'd', 'c', 'b', 'a']

```

### 基本数据类型

整数,浮点数,字符串,布尔值
如果字符串内部既包含'又包含"怎么办？可以用转义字符\来标识

```py
word = 'I\'m \"OK\"!'
```

\n 表示换行，\t 表示制表符，字符\本身也要转义，所以\\表示的字符就是\
Python 还允许用 r''表示''内部的字符串默认不转义
用\n 写在一行里不好阅读，为了简化，Python 允许用'''...'''的格式表示多行内容

布尔值,True,Flase
布尔值可以用 and、or 和 not 运算。

空值 None

变量名必须是大小写英文、数字和\_的组合，且不能用数字开头

动态语言,更灵活,重复复制无需考虑变量类型

特殊变量
\_\_author**,\_\_name**就是特殊变量

```py
a = 'ABC'
# 在内存中创建了一个'ABC'的字符串；
# 在内存中创建了一个名为a的变量，并把它指向'ABC'
```

除法(/),地板除(//)和取余(%)
取幂运算(\*\*)

```py
2**10
# 1024
```

字符串转数字

```py
s="1234"
birth = int(s)
```

### 其他数据类型

1. list
   list 是一个可变的有序表

- 初始化 list= ["apple","banana","peach"]
- 元素的个数 len(list)
- 元素索引访问 list[0]

```py
# 常见内置函数
list[len(list) - 1] = list[-1]
append
insert(i,element)
pop
pop(i)
```

2. tuple
   不可变的有序列表
   classmates = ('Michael', 'Bob', 'Tracy')
   没有 append,insert
   只有 1 个元素的 tuple 定义时必须加一个逗号,，来消除歧义
   t = (1,)

3. dict
   dict 全称 dictionary，在其他语言中也称为 map，使用键-值（key-value）存储，具有极快的查找速度
   根据 key 按一定规则找到 value 的内存,list 是从头查到尾,查找效率比 list 高
   要删除一个 key，用 pop(key)方法，对应的 value 也会从 dict 中删除
   dict 获取 key 用 keys()

```py
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
print(d['Michael'])
d.pop('Bob')
```

避免 key 不存在的错误
通过 in 判断 key 是否存在
通过 dict 提供的 get()方法，如果 key 不存在，可以返回 None，或者自己指定的 value

```py
d.get('Thomas')
d.get('Thomas', -1)
```

dict 需要占用大量内存，是空间换时间的解法
key 是不可变对象，可以为字符串,整数，不能为 list 4. set
set 可以看成数学意义上的无序和无重复元素的集合

```py
s1 = set([1, 2, 3])
s2 = set([2, 3, 4])
s1 & s2
{2, 3}
s1 | s2
{1, 2, 3, 4}
```

### 函数

(https://docs.python.org/3/library/functions.html)[python内置函数]
常见的: dir(),help(),type(),range()

- range(start, stop[, step])
  range(10) ，从 0 开始不包含 10
  range(1,10)，从 1 开始不包含 10
  Python3 range() 函数返回的是一个可迭代对象（类型是对象），而不是列表类型， 所以打印的时候不会打印列表。
  Python3 list() 函数是对象迭代器，可以把 range()返回的可迭代对象转为一个列表，返回的变量类型为列表。
  Python2 range() 函数返回的是列表。

- enumerate(sequence, [start=0])

用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。

```py
seq = ['one', 'two', 'three']
for i, element in enumerate(seq):
    print i, element
# 0 one
# 1 two
# 2 three
```

1. 数据类型转换
   int('123') float('12.34') str(1.23) bool(1) bool('')
2. 定义语句

```py
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```

3. 空函数(待实现的时候占个位，不报错)

```py
def nop():
    pass
```

4. 抛异常

```py
raise TypeError('bad operand type')
```

5. 多个返回值
   返回值是一个 tuple！但是，在语法上，返回一个 tuple 可以省略括号，而多个变量可以同时接收一个 tuple，按位置赋给对应的值，所以，Python 的函数返回多值其实就是返回一个 tuple，但写起来更方便。
   函数执行完毕也没有 return 语句时，自动 return None

6. 参数类型

- 默认参数
- 可变参数
  一个星是基本类型
  二个星是 dict
- 关键字参数
  dict 的解构

```py
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
person('Adam', 45, gender='M', job='Engineer')
# name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}
```

- 命名关键字参数
  和关键字参数\*_kw 不同，命名关键字参数需要一个特殊分隔符_，\*后面的参数被视为命名关键字参数。

```py
def person(name, age, *, city="BeiJing", job):
    print(name, age, city, job)
```

7. 参数顺序
   参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。

### 高级特性

- 切片

```py
L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']
# 取前N个元素
r = []
n = 3
for i in range(n):
    r.append(L[i])
# 一行切片
L[0:3]
```

- 迭代
  在 Python 中，迭代是通过 for ... in 来完成的
  默认情况下，dict 迭代的是 key。如果要迭代 value，可以用 for value in d.values()，如果要同时迭代 key 和 value，可以用 for k, v in d.items()
  如何判断一个对象是可迭代对象呢？方法是通过 collections.abc 模块的 Iterable 类型判断
  Python 内置的 enumerate 函数可以把一个 list 变成索引-元素对，这样就可以在 for 循环中同时迭代索引和元素本身

```py
from collections.abc import Iterable
isinstance('abc', Iterable) # str是否可迭代
# True
isinstance([1,2,3], Iterable) # list是否可迭代
# True
isinstance(123, Iterable) # 整数是否可迭代
# False

for i, value in enumerate(['A', 'B', 'C']):
    print(i, value)
```

- 列表生成式
  在一个列表生成式中，for 前面的 if ... else 是表达式，而 for 后面的 if 是过滤条件，不能带 else

```py
[x * x for x in range(1, 11) if x % 2 == 0]
# [4, 16, 36, 64, 100]
[m + n for m in 'ABC' for n in 'XYZ']
# ['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
[x if x % 2 == 0 else -x for x in range(1, 11)]
# [-1, 2, -3, 4, -5, 6, -7, 8, -9, 10]
```

- 生成器
  如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的 list，从而节省大量的空间。在 Python 中，这种一边循环一边计算的机制，称为生成器：generator。
  要创建一个 generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个 generator

```py
g = (x * x for x in range(10))
for n in g:
    print(n)
# 0 1 4 9 16 25
```

- yield 和 generator
  如果一个函数定义中包含 yield 关键字，那么这个函数就不再是一个普通函数，而是一个 generator 函数，调用一个 generator 函数将返回一个 generator
  普通函数是顺序执行，遇到 return 语句或者最后一行函数语句就返回。而变成 generator 的函数，在每次调用 next()的时候执行，遇到 yield 语句返回，再次执行时从上次返回的 yield 语句处继续执行。
  生成器不但可以作用于 for 循环，还可以被 next()函数不断调用并返回下一个值，直到最后抛出 StopIteration 错误表示无法继续返回下一个值了。

```py
# 斐波那契数列
def fab(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b      # 使用 yield
        # print b
        a, b = b, a + b
        n = n + 1

for n in fab(5):
    print n

# 使用 isgeneratorfunction 判断是否generator函数

from inspect import isgeneratorfunction
isgeneratorfunction(fab) # True


```

- 迭代器
  凡是可作用于 for 循环的对象都是 Iterable 类型；
  凡是可作用于 next()函数的对象都是 Iterator 类型，它们表示一个惰性计算的序列；
  集合数据类型如 list、dict、str 等是 Iterable 但不是 Iterator，不过可以通过 iter()函数获得一个 Iterator 对象。
  Python 的 for 循环本质上就是通过不断调用 next()函数实现的

```py
for x in [1, 2, 3, 4, 5]:
    pass
# 等价于======
# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```

### 异常处理

```py
try:
    print('try...')
    r = 10 / int('2')
    print('result:', r)
except ValueError as e:
    print('ValueError:', e)
except ZeroDivisionError as e:
    print('ZeroDivisionError:', e)
else:
    print('no error!')
finally:
    print('finally...')
print('END')
```

1. Python 所有的错误都是从 BaseException 类派生的，常见的错误类型和继承关系看这里：
   https://docs.python.org/3/library/exceptions.html#exception-hierarchy
2. 如果错误没有被捕获，它就会一直往上抛，最后被 Python 解释器捕获，打印一个错误信息，然后程序退出
3. Python 内置的 logging 模块可以非常容易地记录错误信息

```py
# err_logging.py

import logging

def foo(s):
    return 10 / int(s)

def bar(s):
    return foo(s) * 2

def main():
    try:
        bar('0')
    except Exception as e:
        logging.exception(e)

main()
print('END')
```

4. 错误是 class，捕获一个错误就是捕获到该 class 的一个实例。因此，错误并不是凭空产生的，而是有意创建并抛出的。Python 的内置函数会抛出很多类型的错误，我们自己编写的函数也可以抛出错误。
   如果要抛出错误，首先根据需要，可以定义一个错误的 class，选择好继承关系，然后，用 raise 语句抛出一个错误的实例

```py
# err_raise.py
class FooError(ValueError):
    pass

def foo(s):
    n = int(s)
    if n==0:
        raise FooError('invalid value: %s' % s)
    return 10 / n

foo('0')
```

5. raise 语句如果不带参数，就会把当前错误原样抛出。此外，在 except 中 raise 一个 Error，还可以把一种类型的错误转化成另一种类型

### 高阶函数

- map/reduce

map()函数接收两个参数，一个是函数，一个是 Iterable，返回一个 list
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)

```py
from functools import reduce
def fn(x, y):
    return x * 10 + y
def char2num(s):
    digits = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
    return digits[s]
reduce(fn, map(char2num, '13579'))
# (((1*10 +3)*10 + 5)*10+7)*10+9
# 13579

```

- filter

  过滤一个子元素符合筛选的 list

```py
def not_empty(s):
    return s and s.strip()

list(filter(not_empty, ['A', '', 'B', None, 'C', '  ']))
# 结果: ['A', 'B', 'C']
```

- sorted

```py
sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
# ['Zoo', 'Credit', 'bob', 'about']
```

- 返回函数

返回一个函数时，牢记该函数并未执行，返回函数中不要引用任何可能会变化的变量

```py
def count():
    def f(j):
        def g():
            return j*j
        return g
    fs = []
    for i in range(1, 4):
        fs.append(f(i)) # f(i)立刻被执行，因此i的当前值被传入f()
    return fs

f1, f2, f3 = count()
print(f1()) # 1
print(f2()) # 4
print(f3()) # 9
```

- 匿名函数

lambda 入参:返回值 la

```py
f = lambda x: x * x
f # <function <lambda> at 0x101c6ef28>
f(5) # 25
```

- 装饰器

1. 装饰器函数

用 functools.wraps 实现

```py
from functools import wraps

def logit(func):
    @wraps(func)
    def with_logging(*args, **kwargs):
        print(func.__name__ + " was called")
        return func(*args, **kwargs)
    return with_logging

@logit
def addition_func(x):
   """Do some math."""
   return x + x


result = addition_func(4)
# Output: addition_func was called
```

2. 装饰器类

```py
from functools import wraps

class logit(object):
    def __init__(self, logfile='out.log'):
        self.logfile = logfile

    def __call__(self, func):
        @wraps(func)
        def wrapped_function(*args, **kwargs):
            log_string = func.__name__ + " was called"
            print(log_string)
            # 打开logfile并写入
            with open(self.logfile, 'a') as opened_file:
                # 现在将日志打到指定的文件
                opened_file.write(log_string + '\n')
            # 现在，发送一个通知
            self.notify()
            return func(*args, **kwargs)
        return wrapped_function

    def notify(self):
        # logit只打日志，不做别的
        pass
@logit()
def myfunc1():
    pass
```

- 偏函数

普通函数封装和偏函数封装应用场景：

1. 首先都是对他人提供的函数做的二次封装
2. 普通封装适用于在调用方法前后要做些预处理操作
3. 偏函数适用于在调用方法前设置一些默认参数，不能添加预处理操作

```py
# 准备一个原函数
def info(name,age):
    print("姓名:{},年龄{}".format(name,age))

from functools import partial
pinfo = partial(info,age=18) # 定义偏函数时设置默认值

pinfo("xuepl") # 位置传参
pinfo(name = "xuepl") # 关键字传参都可以`
```

### 面向对象

- 类和实例

```py
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score

    def print_score(self):
        print('%s: %s' % (self.name, self.score))
```

和普通的函数相比，在类中定义的函数只有一点不同，就是第一个参数永远是实例变量 self，并且，调用时，不用传递该参数。除此之外，类的方法和普通函数没有什么区别，所以，你仍然可以用默认参数、可变参数、关键字参数和命名关键字参数。

- 访问限制
  在 Python 中，变量名类似\_\_xxx\_\_的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是 private 变量，所以，不能用\_\_name\_\_、\_\_score\_\_这样的变量名。

有些时候，你会看到以一个下划线开头的实例变量名，比如\_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

- 类特殊成员属性和方法

1. \_\_new\_\_
   会优先 \_\_init\_\_() 初始化方法被调用
   Python 中大量使用 \_\_new\_\_() 方法且合理的，就是 MetaClass 元类。
2. \_\_repr\_\_
   自定义输出实例化对象时的信息

```py
class CLanguage:
    def __init__(self):
        self.name = "C语言中文网"
        self.add = "http://c.biancheng.net"
    def __repr__(self):
        return "CLanguage[name="+ self.name +",add=" + self.add +"]"
clangs = CLanguage()
print(clangs)
```

3. \_\_del\_\_()
   手动销毁,python 有自动的垃圾回收机制

   Python 采用自动引用计数（简称 ARC）的方式实现垃圾回收机制。该方法的核心思想是：每个 Python 对象都会配置一个计数器，初始 Python 实例对象的计数器值都为 0，如果有变量引用该实例对象，其计数器的值会加 1，依次类推；反之，每当一个变量取消对该实例对象的引用，计数器会减 1。如果一个 Python 对象的的计数器值为 0，则表明没有变量引用该 Python 对象，即证明程序不再需要它，此时 Python 就会自动调用 \_\_del\_\_() 方法将其回收。

4. \_\_dir\_\_()
   返回一个包含有所有属性名和方法名的有序列表
5. \_\_dict\_\_
   查看对象内部所有属性名和属性值组成的字典

```py
class CLanguage:
    a = "aaa"
    b = 2
    def __init__ (self):
        self.name = "C语言中文网"
        self.add = "http://c.biancheng.net"
#通过类实例对象调用 __dict__
clangs = CLanguage()
print(clangs.__dict__)
clangs.__dict__['name'] = "Python教程"
print(clangs.name)
```

6. setattr,getattr,hasattr

- hasattr(obj, name)判断一个对象里面是否有 name 属性或者 name 方法，返回 BOOL 值，有 name 特性返回 True， 否则返回 False。
- getattr(obj, name[, default]) 获取对象 object 的属性或者方法，如果存在打印出来，如果不存在，打印出默认值，默认值可选。返回的对象的方法，返回的是方法的内存地址，可执行
- setattr(obj, name, value) 给对象的属性赋值，若属性不存在，先创建再赋值。

```py
class test():
    name="lili"
    def run(self):
        return "HelloWorld"
t = test()
getattr(t,"name") # 获取name属性，存在就打印
hasattr(t,"age") # False
setattr(t,"age","18")
hasattr(t,"age") # True
```

7. issubclass 和 isinstance

```py
class A: pass
class B(A): pass

    b= B()

    print( isinstance(b,B))   #True

    print( isinstance(b,A))   #True

    print( issubclass(B,A))  #True

    print( issubclass(B,object))  #True
```

8. \_\_call\_\_
   该方法的功能类似于在类中重载 () 运算符，使得类实例对象可以像调用普通函数那样，以“对象名()”的形式使用。
   函数是个对象
   所有的函数都是可调用对象。
   一个类实例也可以变成一个可调用对象，只需要实现一个特殊方法\_\_call\_\_()。

```py
class Person(object):
    def __init__(self, name, gender):
        self.name = name
        self.gender = gender

    def __call__(self, friend):
        print 'My name is %s...' % self.name
        print 'My friend is %s...' % friend
p = Person('Bob', 'male')
p('Tim')
# My name is Bob
# My friend isTim
```

9. \_\_iter\_\_ 和 \_\_next\_\_

```py
class Fib:
    def __init__(self, max):
        self.max = max
    def __iter__(self):
        print('__iter__ called')
        self.a = 0
        self.b = 1
        return self
    def __next__(self):
        print('__next__ called')
        fib = self.a
        if fib > self.max:
            raise StopIteration
        self.a, self.b = self.b, self.a + self.b
        return fib

for i in Fib(3):
    print(i)
# 输出
__iter__ called
__next__ called
0
__next__ called
1
__next__ called
1
__next__ called
2
__next__ called
```

定义 \_\_iter\_\_ 表示这个类是一个迭代器（iterator）。它只在迭代开始的时候运行一次。返回的是对象本身。这里还给顺手给对象添加了 a 和 b 两个属性。接下来就是循环调用 \_\_next\_\_ 直到遇到 raise StopIteration 为止。

- 继承多态

```py
class Animal(object):
    def run(self):
        print('Animal is running...')
class Dog(Animal):
    def run(self):
        print('Dog is running...')

class Cat(Animal):

    def run(self):
        print('Cat is running...')
        print('Animal is running...')
```

- 获取对象信息

1. type()
2. isinstance()判断一个变量是否是某个类型
3. dir()获得一个对象的所有属性和方法，返回一个包含字符串的 list

```py
len('ABC') # 3
'ABC'.__len__() # 3
```

通过内置的一系列函数，我们可以对任意一个 Python 对象进行剖析，拿到其内部的数据。要注意的是，只有在不知道对象信息的时候，我们才会去获取对象信息。如果可以直接写：

```py
sum = obj.x + obj.y
```

就不要写：

```py
sum = getattr(obj, 'x') + getattr(obj, 'y')
```

- 实例属性和类属性

  1. 实例属性属于各个实例所有，互不干扰；

  2. 类属性属于类所有，所有实例共享一个属性；

  3. 不要对实例属性和类属性使用相同的名字，否则将产生难以发现的错误。

### 面向对象高级

1. \_\_slot\_\_

```py
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
s = Student() # 创建新的实例
s.name = 'Michael' # 绑定属性'name'
s.age = 25 # 绑定属性'age'
s.score = 99 # 绑定属性'score',报错
# Traceback (most recent call last):
#  File "<stdin>", line 1, in <module>
# AttributeError: 'Student' object has no attribute 'score'
```

由于'score'没有被放到\_\_slots\_\_中，所以不能绑定 score 属性，试图绑定 score 将得到 AttributeError 的错误。
使用\_\_slots\_\_要注意，\_\_slots\_\_定义的属性仅对当前类实例起作用，对继承的子类是不起作用的

2. @property

@property 广泛应用在类的定义中，可以让调用者写出简短的代码，同时保证对参数进行必要的检查，这样，程序运行时就减少了出错的可能性。
定义只读属性，只定义 getter 方法，不定义 setter 方法就是一个只读属性

```py
class Student(object):

    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        self._birth = value

    @property
    def age(self):
        return 2015 - self._birth
```

方法名和实例变量名不要重复。。。

3. 多重继承

```py
class allbase:
    def eat(self):
        print("allbase is eating")
class pingbase(allbase):
    def eat(self):
        print("pigbase is eating")
class pig(pigbase):
    def eat(self):
        print("pig is eating")
class dogbase(allbase):
    pass
class dog(dogbase):
    def drink(self):
        print("dong is drink")
class trutle(dog,pig):
    def sleep(self):
        print("turtle is sleeping")
mrn = turtle()
mrn.eat()
```

当多继承的几个父类都有同样的方法的时候，子类执行的是先继承父类的方法，比如下面的例子中 pig 和 dog 类中都有 eat 的方法，但是 turtle 在继承的时候先继承了 dog 类，所以在 turtle 实例调用 eat 方法的时候，使用的是 dog 中的 eat 方法。
当前子类名称.mro()，返回是一个列表，列表中的顺序就是当先子类查找父类中方法的顺序。

4. 定制类
   (定制类)[https://docs.python.org/3/reference/datamodel.html#special-method-names]

5. 元类

- type()

```py
def fn(self, name='world'): # 先定义函数
    print('Hello, %s.' % name)

Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
h = Hello()
h.hello()

# Hello, world.
print(type(Hello))
# <class 'type'>
print(type(h))
# <class '__main__.Hello'>
```

要创建一个 class 对象，type()函数依次传入 3 个参数：

class 的名称；
继承的父类集合，注意 Python 支持多重继承，如果只有一个父类，别忘了 tuple 的单元素写法；
class 的方法名称与函数绑定，这里我们把函数 fn 绑定到方法名 hello 上。
通过 type()函数创建的类和直接写 class 是完全一样的，因为 Python 解释器遇到 class 定义时，仅仅是扫描一下 class 定义的语法，然后调用 type()函数创建出 class。

- metaclass

6. 枚举类

   枚举类型定义一个 class 类型，然后，每个常量都是 class 的一个唯一实例。Python 提供了 Enum 类来实现这个功能

```py
from enum import Enum

Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))

for name, member in Month.__members__.items():
    print(name, '=>', member, ',', member.value)
```

从 Enum 派生出自定义类

```py
from enum import Enum, unique

@unique
class Weekday(Enum):
    Sun = 0 # Sun的value被设定为0
    Mon = 1
    Tue = 2
    Wed = 3
    Thu = 4
    Fri = 5
    Sat = 6
```

### 模块

#### 使用模块

Click 是一个简洁好用的 Python 模块，它能用尽量少的代码实现漂亮的命令行界面。它不仅开箱即用、还能支持高度自定义的配置。

#### 内置模块

- datetime
- collections
  1. namedtuple
  2. deque
  3. defaultdict
  4. OrderedDict
  5. ChainMap
  6. Counter
- base64
- struct
- hashlib
- hmac
- itertools
- contextlib
- urllib
- XML
- HTMLParser

#### 三方模块

- venv(Python3.3,自带的库)
  给每个项目建议一个独立的环境
  venv 为应用提供了隔离的 Python 运行环境，解决了不同应用间多版本的冲突问题。

1.  Python 虚拟环境 venv 用法详解

```shell
python -m venv [环境名称]
```
- 安装pyTorch

(安装pyTorch)[https://pytorch.org/get-started/locally/]

```shell
python -c "import torch; print(torch.__version__)"
```

2. 进入创建的项目

```shell
python -m venv test
./test/scripts/activate.bat # 进入创建的项目
./test/scripts/deactivate.bat # 退出环境
```

## 数据库

内置了 sqlite3
在 Python 中使用 SQLite，不需要安装任何东西，直接使用。

## 发包到 pypi

1. 注册 github
2. 注册 pypi
3. 准备包,包目录
   /# tree
   googletrans:{
   \_\_init\_\_.py
   trans_google.py
   }
   README.md--项目介绍
   LICENSE
   setup.py--打包的元数据信息
   setup.cfg--setuptools 的配置文件
   .pypirc--用于上传包到 pypi 时的权限验证
4. 打包

```py
pip install setuptools # 安装setuptools
pip install twine # 安装twine
python setup.py build # 生成build的目录，目录层级是build/lib/test,可以在此查看打包代码是否完整
python setup.py sdist # 生成dist目录,在该目录下生成压缩包文件.tar.gz
twine upload dist/XXXXX.tar.gz # 上传刚刚打包好的压缩包
pip install XXXXX # 测试安装
```

## 静态类型检查

Mypy 是 Python 的一个静态类型检查工具

## 读写源数据

### pandas

1. pandas.read_csv(filepath_or_buffer, header="infer", index_col=None, usecols=None, nrows=None)
2. pandas.read_excel(io, sheet_name=0,header=0,index_col=None)
3. pandas.read_sql(sql, con, index_col=None)

```py
import pandas as pd
df = pd.read_csv('/data/a.csv')
df.to_csv(path_or_buf, header=True, index=True)

# 读取a.xlsx表sheet_name是‘测试数据’的数据
df = pd.read_excel('data/a.xlsx', sheet_name='测试数据')
with pd.ExcelWriter('/data/b.xlsx') as writer:
    df.to_excel(writer, sheet_name='测试数据',index=False)

from sqlalchemy import create_engine
# 填写mysql的连接url
con = "mysql+pymysql://{user}:{pwd}@{host}:{port}/{db}?charset=utf8"
engine = create_engine(con, connect_args={'connect_timeout': 20})

sql = "select id, name from users where id"
df = pd.read_sql(sql, con)

# 填写mysql的连接url
con = "mysql+pymysql://{user}:{pwd}@{host}:{port}/{db}?charset=utf8"
engine = create_engine(con, connect_args={'connect_timeout': 20})

sql = "select id, name from users where id"
df = pd.read_sql(sql, con)

# 数据写入到a表
df.to_sql(sql, con=con, if_exists="append", index=False,index_label=False)
```
