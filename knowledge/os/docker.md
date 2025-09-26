---
title: docker基础
date: 2017-07-22 11:23:51
categories: 运维 #文章分类
tags: [docker] #文章标签，可以一次添加多个标签
---

docker...

<!-- more -->

## wsl2 ubuntu安装docker

```sh

ip addr show eth0 #查看wsl2的ip

sudo apt update

sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] http://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io

sudo docker --version

sudo usermod -aG docker $USER

# 配置阿里云镜像

#安装docker-compose

sudo curl -L "https://github.com/docker/compose/releases/download/v2.14.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
```



## docker

```bash
docker images
docker ps -a # 查看所有容器
docker start container name # 启动容器
docker inspect container name # 查看容器信息
docker exec -it container name /bin/bash # 进入容器
```

```bash
docker save -o front.tar   registry.agree.com.cn:5000/aops-long-front:latest
```

### docker network

(docker network科普)[https://zhuanlan.zhihu.com/p/639664175]

### Dockerfile

```bash
docker build -t  testdockerfile:v1.0  ./
```

### docker compose

在 docker-compose.yml 目录下运行

```bash
docker-compose --version
docker-compose up # 启动服务
docker-compose up -d # 忽略输出，-d 参数是后台启动 会读取当前工作目录下的docker-compose.yml 文件
docker-compose ps # 可以至查看当前项目的容器状态
docker-compose exec nginx /bin/bash # 容器执行命令
docker-compose stop # 停止容器
```

- build 构建（重新构建）项目中的服务容器
- config 检测 compose 文件的错误
- up 创建并启动所有service容器
- down 停止容器并移除所有的容器和网络
- images 列出项目中所包含的镜像
- logs 查看服务容器的日志
- kill 发送 SIGKILL 信号来强制停止服务容器
- port 查看某个容器端口所映射的公共端口
- ps 列出项目中目前的所有容器
- restart 重启项目中的服务
- rm 删除所有停止状态的服务容器
- run 在指定服务上运行一个命令
- scale 设置指定服务运行的容器个数
- stop 停止处于运行状态的容器
- start 启动被 stop 的服务容器
- top 查看各个服务容器内运行的进程
- pause 暂停一个服务容器
- unpause 恢复处于暂停状态中的服务

  对于 docker-compose 有两个基本的概念：

- 服务(service)：一个应用容器，即 docker 容器，比如之前所说的 mysql 容器、nginx 容器
- 项目(project)：由一组关联的应用容器组成的一个完整业务单元，比如上面所讲的由 mysql、web app、nginx 容器组成的网站。docker-compose 面向项目进行管理。

Compose 通过一个配置文件来管理多个 Docker 容器，在配置文件中，所有的容器通过 services 来定义，然后使用 docker-compose 脚本来启动，停止和重启应用，和应用中的服务以及所有依赖服务的容器，非常适合组合使用多个容器进行开发的场景

(docker-compose 服务启动顺序控制)[https://www.cnblogs.com/wang_yb/p/9400291.html]
(示例项目)[https://github.com/zhengqingya/xiao-xiao-su]

### 镜像的导入导出

```bash
# 打包单个镜像
docker export f299f501774c > hangger_server.tar
docker import - new_hangger_server < hangger_server.tar

# docker-compose 打包包含多个基础镜像
docker save 0fdf2b4c26d3 > hangge_server.tar # 对标export
docker load < hangge_server.tar # 对标import
```

1. 用 export 容器 id 和 import - 新名称
2. 用 save 容器 id 和 load
   - 1 和 2 不可混用
   - 区别: docker save 可以一个文件打包**多个镜像**，还包含着镜像历史，体积比较大，不能对载入的镜像重命名,docker export 相反
   - 运行场景：docker export 的应用场景：主要用来制作**基础镜像**，比如我们从一个 ubuntu 镜像启动一个容器，然后安装一些软件和进行一些设置后，使用 docker export 保存为一个基础镜像。然后，把这个镜像分发给其他人使用，比如作为基础的开发环境。
     docker save 的应用场景：如果我们的应用是使用 docker-compose.yml 编排的多个镜像组合，但我们要部署的客户服务器并不能连外网。这时就可以使用 docker save 将用到的镜像打个包，然后拷贝到客户服务器上使用 docker load 载入。

## nginx 的 dockerfile 示例

1. dockerfile

```
FROM nginx:latest

# 删除默认的nginx.conf文件
# RUN rm /etc/nginx/nginx.conf

# 将本地的nginx.conf文件复制到容器中
COPY nginx.conf /etc/nginx/
# 复制dist目录到容器中
COPY dist/ /www/

# 暴露8088端口
EXPOSE 8088

# 启动Nginx服务器
CMD ["nginx", "-g", "daemon off;"]

```

2. 启动命令

```shell
docker run --name firefly-styleguide -p 6060:6060 \
-v /home/agree/firefly-web/styleguide/guide:/www \
-v /home/agree/firefly-web/styleguide/nginx.conf:/etc/nginx/nginx.conf \
-d nginx-custom nginx -g "daemon off;"
```

### portainer

Portainer 是一个轻量级的容器管理工具，可以帮助用户在 Docker 环境中管理容器、镜像、卷和网络等资源。它提供了一个直观的 Web 界面，使得用户可以方便地进行容器的部署、监控和维护，而无需使用 Docker 命令行工具。
