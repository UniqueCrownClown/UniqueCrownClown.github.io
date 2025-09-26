---
title: linux命令
date: 2017-07-21 11:23:51
categories: 运维 #文章分类
tags: [linux] #文章标签，可以一次添加多个标签
---

linux shell...

<!-- more -->

### deepin 尝试

    sudo
    curl or wget should be installed
    yum -y install wget
    systemctl stop nginx.service
    systemctl start nginx.service
    netstat -antp | grep :80
    netstat -ano|findstr 8080

### linux 常见

<!-- ![](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed/hexo/knowledge/linuxPic/order.png) -->

[![XpB8hj.png](https://s1.ax1x.com/2022/05/23/XpB8hj.png)](https://imgtu.com/i/XpB8hj)

#### 常见 shell

```bash
netstat -ano  | findstr 8080
taskkill /?
whereis xxx
lsb_release -a # 查看版本
sudo apt-get update # 更新软件
# systemd 是 Linux 下的一款系统和服务管理器，兼容 SysV 和 LSB 的启动脚本
# 检视和控制systemd的主要命令是systemctl

zip -r xxx.zip ./* #压缩服务器上当前目录的内容为xxx.zip文件

unzip filename.zip # 解压zip文件到当前目录

tar -czvf 压缩文件名.tar.gz 要压缩的文件或目录 #压缩文件

tar -xzvf 压缩文件名.tar.gz #解压缩,z表示用gzip解压缩,v表示显示详细的提取过程

tar -xf 压缩文件名.tar #解压tar文件

chmod -R 775 <directory-name> #给递归目录的群组赋予权限
chmod g+w <directory-name> #给目录的群组赋予权限
ls -l <directory-name> # 查看所有者和权限
sudo chown user1:group1 <directory-name> #设置目录的所有者和群组
sudo chown -R user1:group1 <directory-name> #递归设置目录的所有者和群组

touch test.txt
mkdir test
ls -a # 显示隐藏文件
ls -l # 长列表模式

# 输入的内容写入文件
cat << EOF > a.txt 

```

#### ubuntu 静态 dns 解析

    1. /etc/network/interfaces (dns-nameservers 114.114.114.114 8.8.8.8)
    2. /etc/NetworkManager/NetworkManager.conf (dns=119.29.29.29)
    3. /etc/resolvconf/resolv.conf.d/tail (nameserver=223.5.5.5)

#### host 添加

Linux 系统下 hosts 文件位置：/etc/hosts
如果是单条可以直接执行命令行：
echo "10.13.83.43 yun.xiong.cn">> /etc/hosts

#### find 命令

grep 是查找匹配条件的行，find 是搜索匹配条件的文件

语法：find 起始目录 寻找条件 操作
说明：find 命令从指定的起始目录开始，递归地搜索其各个子目录，查找满足寻找条件的文件并对之采取相关的操作。

```sh
find dirName -options fileName　　#options 是 find 的可选项，如：-name、-size、-mtime、-user、-group、-type、-perm、-exec、-o、-and、-print、-delete

find / -name httpd.conf　　#在根目录下查找文件httpd.conf，表示在整个硬盘查找

find /etc -name httpd.conf 　　#在/etc 目录下文件 httpd.conf
find /etc -name '_srm_'　　#使用通配符*(0 或者任意多个)。表示在/etc 目录下查找文件名中含有字符串‘srm’的文件
find . -name 'srm*' 　　#表示当前目录下查找文件名开头是字符串‘srm’的文件
find /tmp -size +10000c -and -mtime +2 　　#在/tmp 目录下查找大于 10000 字节并在最后 2 分钟内修改的文件
find / -user fred -or -user george 　　#在/目录下查找用户是 fred 或者 george 的文件文件
find /tmp ! -user panda 　　#在/tmp 目录中查找所有不属于 panda 用户的文件
```

#### grep 常见命令

```shell
grep 'test' d\*　　#显示所有以 d 开头的文件中包含 test 的行
grep ‘test’ aa bb cc 　　 #显示在 aa，bb，cc 文件中包含 test 的行
grep ‘[a-z]\{5\}’ aa 　　#显示所有包含每行字符串至少有 5 个连续小写字符的字符串的行
grep magic /usr/src 　　#显示/usr/src 目录下的文件(不含子目录)包含 magic 的行
grep -r magic /usr/src 　　#显示/usr/src 目录下的文件(包含子目录)包含 magic 的行
grep -C 5 "yaml" ruoyi-admin.log # 在 ruoyi-admin.log 文件中查找字符串 yaml，并显示 5 行上下文
```

### 防火墙

```sh
firewall-cmd --zone=public --permanent --add-port=8080/tcp
firewall-cmd --zone=public --list-ports # 查看端口
```


### vim 命令

vimtutor

    hjkl左下上有
    0是跳到行首
    dd删除整行
    :r(replace)
     使用v选择高亮的部分
     使用esc进入正常模式
     使用操作符 y 复制文本，使用 p 粘贴文本
     使用操作符 i 插入文本，使用 u 撤销操作
    w 	保存不退出
    w!	强制保存
    wq	保存退出
    wq!
    q 	退出
    q!	强制退出

<!-- ![](https://cdn.jsdelivr.net/gh/UniqueCrownClown/pic-bed/hexo/knowledge/linuxPic/vim.png) -->

[![XpB3NQ.png](https://s1.ax1x.com/2022/05/23/XpB3NQ.png)](https://imgtu.com/i/XpB3NQ)

### nginx 配置基础

[nginx 内容](https://juejin.cn/post/7007346707767754765)


有时会出现bind() to 0.0.0.0:20990 failed (13: Permission denied)的问题

可能是SELinux的问题

```bash
getenfoce
setforece 0
setforece 1
```

### 编译器

![](https://img.hellogithub.com/article/dGc4t91uqSls3Bp_1652843793.png)
CPU 架构支持把二进制操作映射作为一种更容易阅读的语言——汇编语言。
虽然汇编语言非常低级，但是它可以转换为二进制代码，这种转换主要靠的是“汇编器”

高级语言-->编译器-->汇编语言-->汇编器-->机器代码(二进制代码)

#### 汇编语言的基础知识

特点

- 与硬件相关，每一种处理器都有相应的汇编语言
- 与机器码相关，指令通常与机器码一一对应
- 家用电脑使用 intel/amd 处理器，对应 x86-x64 汇编语言

作用

- 深入理解硬件工作原理；
- 充分利用计算机特性（如 SIMD，即单指令多数据）；
- 开发操作系统内核、驱动程序等；
- 优化程序

#### unicode 和 ascii 的区别

1. ASCII 编码是 1 个字节，而 Unicode 编码通常是 2 个字节
2. ASCII 是单字节编码，无法用来表示中文；而 Unicode 可以表示所有语言
3. 用 Unicode 编码比 ASCII 编码需要多一倍的存储空间

- “可变长编码”的 UTF-8 编码
- ASCII 编码实际上可以被看成是 UTF-8 编码的一部分，所以，大量只支持 ASCII 编码的历史遗留软件可以在 UTF-8 编码下继续工作

- 在计算机内存中，统一使用 Unicode 编码，当需要保存到硬盘或者需要传输的时候，就转换为 UTF-8 编码。
- 用记事本编辑的时候，从文件读取的 UTF-8 字符被转换为 Unicode 字符到内存里，编辑完成后，保存的时候再把 Unicode 转换为 UTF-8 保存到文件

ASCII 码使用指定的 7 位或 8 位二进制数组合来表示 128 或 256 种可能的字符。标准 ASCII 码也叫基础 ASCII 码，使用 7 位二进制数（剩下的 1 位二进制为 0）来表示所有的大写和小写字母，数字 0 到 9、标点符号， 以及在美式英语中使用的特殊控制字符。其中最后一位用于奇偶校验。

Unicode 把所有语言都统一到一套编码里，这样就不会再有乱码问题了。
Unicode 最常用的是用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要 4 个字节）。现代操作系统和大多数编程语言都直接支持 Unicode。

UTF-8 是 Unicode 的实现方式之一
