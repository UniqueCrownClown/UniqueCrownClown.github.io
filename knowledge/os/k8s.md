---
title: k8s基础
date: 2018-07-22 11:23:51
categories: 运维 #文章分类
tags: [k8s] #文章标签，可以一次添加多个标签
---

k8s...

<!-- more -->

### k8s 大规模容器集群管理工具

#### 环境搭建

1. 三台机器，其中一个 master 节点，两个 worker 工作节点，保证每台机器间能使用内网 ip 互通
2. 每台机器安装 docker 保证容器运行环境，然后安装核心的三个部件 kubelet(工作节点上的一个代理组件,运行在每个节点上)、kubectl(命令行工具)、kubeadm(初始化集群工具)
3. 使用 kubeadm 引导集群

- 下载 k8s 所需的镜像
- 添加 k8s 中主节点的域名映射
- 初始化 k8s 主节点
- 添加 k8s 集群中的从节点
- 验证集群自动恢复功能

4. 部署 Dashboard

#### 安装软件

1. kubectl

2. helm(k8s 的包管理器)

   Helm 是 Kubernetes 的软件包管理器，类似于 yum、apt 等包管理工具一样，Helm 可以轻松的一键式部署出我们想要的应用

   - 特性;

     - 查找并使用流行的软件，将其打包为 Helm Charts，以便在 Kubernetes 中运行
     - 以 Helm Charts 的形式共享您自己的应用程序
     - 为您的 Kubernetes 应用程序创建可复制的构建
     - 智能地管理您的 Kubernetes 清单文件
     - 管理 Helm 包的发行版

   - Helm Charts: 定义了应用包的结构以及依赖关系；
     Charts 在本质上是一个 **tar 包**，包含了一些 **yaml** 的 template 以及解析 template 需要的 values, 如下图：templates 是 Golang 的 template 模板，values.yaml 里面包含了这个 Charts 需要的值。
   - Helm Registry: 解决了包存储；
     用来负责存储和管理用户的 Charts, 并提供简单的版本管理，与容器领域的镜像仓库类似这个项目是开源的。
   - HelmTiller: 负责将包运行在 Kubernetes 集群中
     负责将 Chart 部署到指定的集群当中，并管理生成的 Release (应用)；
     支持对 Release 的更新，删除，回滚操作；
     支持对 Release 的资源进行增量更新；
   - Release 的状态管理；
     使用 helm install 命令在 Kubernetes 集群中部署的 Chart 称为 Release。
     Helm 中提到的 Release 和我们通常概念中的版本有所不同，这里的 Release 可以理解为 **Helm 使用 Chart 包部署的一个应用实例**。

   - 简单使用

   ```bash
      helm repo list

      # 拉包到本地
      $ helm pull harbor/harbor
      #或者
      $ helm install myharbor ./harbor-1.7.3.tgz
      $ helm list

   ```

   - master-->node-->多个 pod-->多个容器

3. helm 安装 harbor
   一个用于存储和分发 Docker 镜像的企业级 Registry 服务器
   harbor 是一个可以同时存放镜像和 helm charts 包的仓库管理

#### 181 的 k8s 部署

```bash
cd /root/k8s_resource/k8sadmin
docker images |grep frontend
docker build -t harbor.k8s/agree/k8sadmin/frontend:2021072202 .
docker push harbor.k8s/agree/k8sadmin/frontend:2021072202
kubectl get deploy -n k8sadmin |grep front
kubectl get pod -n k8sadmin |grep front
kubectl edit deploy k8sadmin-frontend-deployment -n k8sadmin #直接修改yaml文件中的镜像
# 修改映射的image标签
kubectl get pod -n k8sadmin |grep front
```

#### k8s 部署的 deployment 文件

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: simple-web
  template:
    metadata:
      labels:
        app: simple-web
    spec:
      containers:
        - name: simple-web
          image: simple-web:1.0
          livenessProbe:
            httpGet:
              path: /health
              port: 8080
          readinessProbe:
            httpGet:
              path: /health
              port: 8080
          volumeMounts:
            - name: app-log-vol
              mountPath: /app/logs
            - name: config-vol
              mountPath: /app/conf/application.properties
              subPath: application.properties
          resources:
            requests:
              memory: 2048Mi
              cpu: 2
            limits:
              memory: 2048Mi
              cpu: 2
      volumes:
        - name: app-log-vol
          emptyDir: {}
        - name: config-vol
          configMap:
            name: simple-web-config
            items:
              - key: app-config
                path: application.properties
```

#### k8s 部署的 configMap 文件

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: simple-web-config
data:
  app-config: |-
    app.name=web-name-from-config-web
```

#### k8s 部署的 Service

```yml
apiVersion: v1
kind: Service
metadata:
  name: simple-web
spec:
  type: NodePort
  selector:
    app: simple-web
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 32100
# selector 我们需要填写在 deployment 中定义的 pod 的标签
```

#### k8s 中的 port

- port
  port 是 k8s 集群内部访问 service 的端口，即通过 clusterIP: port 可以从 Pod 所在的 Node. 上访问到 service

- nodePort
  nodePort 是外部访问 k8s 集群中 service 的端口，通过 nodeIP: nodePort 可以从外部访问到某个 service。

- targetPort
  targetPort 是 Pod 的端口，从 port 或 nodePort 来的流量经过 kube-proxy 反向代理负载均衡转发到后端 Pod 的 targetPort 上，最后进入容器。

- containerPort
  containerPort 是 Pod 内部容器的端口，targetPort 映射到 containerPort

#### 获取集群节点

```bash
kubectl get nodes
#查看集群健康状况
kubectl cluster-info
```

```sh
    kubectl create
    kubectl describe #资源类型 资源名称 (查看某个资源的详细信息)
    #要检查 Pod 中容器的状态，你可以使用 kubectl describe pod <pod 名称>。 其输出中包含 Pod 中每个容器的状态
    kubectl logs #资源类型 资源名称 (查看某个资源的日志)
```

1. master 节点组件

   - Api Server：k8s API Server 提供了 k8s 各类资源对象（pod,RC,Service 等）的增删改查及 watch 等 HTTP Rest 接口，是整个系统的数据总线和数据中心。
     对外暴露 K8S 的 api 接口，是外界进行资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制；

   - scheduler 负责资源的调度，按照预定的调度策略将 Pod 调度到相应的机器上；就是监视新创建的 Pod，如果没有分配节点，就选择一个节点供他们运行，这就是 pod 的调度

   - controller manager 负责维护集群的状态，比如故障检测、自动扩展、滚动更新等，它们是处理集群中常规任务的后台线程

   - etcd：kubernetes 的后端数据库，k/v 方式存储，所有的 k8s 集群数据都存放在此处。保存了整个集群的状态

   其中有 kube-scheduler 和 kube-controller-manager 两个组件是有 leader 选举的，这个选举机制是 k8s 对于这两个组件的高可用保障。

2. node 节点组件

   - kubelet 负责维护容器的生命周期，同时也负责 Volume（CSI）和网络（CNI）的管理；

   - kube-proxy 负责为 Service 提供 cluster 内部的服务发现和负载均衡；

   - fluentd 是一个守护进程，它有助于提供集群层面日志 集群层面的日志

3. service

   - service-name = k8sadmin-frontend-service
   - K8s 中的 Service 定义了一个服务的访问入口地址，前端应用可以通过这个入口地址访问其背后一组由 Pod 副本组成的集群实例，Service 与其后端的 Pod 副本集群之间是通过 Label Selector 来实现连接的。
   - type
   - clusterIP：只对集群内部可见
   - nodePort：对外部可见

   ```bash
   kubectl get svc k8sadmin-frontend-service --namespace k8sadmin -o=yaml #查看 service 的详细信息
   ```

4. pod

   - pod 也是 k8s 进行调度的最小单位
   - pod = 根容器 Pause + 多个业务容器(Pod 里多个业务容器共享 Pause 容器的 IP 和 Volume，简化了业务容器之间的通信问题，也解决了文件共享问题。)
   - Pod 有两种类型：普通 Pod 和静态 Pod，
   - 静态 Pod 并不存放在 etcd 存储里，而是存放在某个具体的 Node 里的一个具体文件中，并且只在此 Node 上运行。
   - 普通 Pod 创建之后就会被放在 etcd 中存储，随后被 Master 调度到某个 Node 上并进行绑定，被 Node 上 kubelet 进程实例化成一组相关的 Docker 容器并启动。
   - 默认情况下，Pod 某个容器停止 时，k8s 会自动检测并重启此 Pod，如果所在的 Node 宕机，则会将所有 Pod 重新调度到其他节点上。

   podname = k8sadmin-frontend-deployment-7f489f9684-p7dgl
   command = ls

   ```bash
    # 创建 pod

   kubectl get pods #查看默认命名空间的 pod

   kubectl get pods --namespace liusy

   kubectl get pods -A # 查看所有的 pod

   kubectl get pods k8sadmin-frontend-deployment-7f489f9684-p7dgl -o=wide --namespace k8sadmin # 显示 Pod 更多信息，状态、IP、运行在那个节点等

   kubectl get pods podname -o=yaml # 以 yaml 格式显示 pod 的详细信息


   kubectl exec k8sadmin-frontend-deployment-7f489f9684-p7dgl --namespace k8sadmin -- ls # 执行容器命令(默认使用 pod 的第一个容器执行)

   kubectl exec -it <pod-name> -c <container-name> /bin/bash # 进入容器,进入 pod 的指定容器
   kubectl exec -it k8sadmin-frontend-deployment-7f489f9684-p7dgl -c k8sadmin-frontend --namespace k8sadmin /bin/bash
   ```

#### 容器化编排

- 资源编排 - 负责资源的分配，如限制 namespace 的可用资源，scheduler 针对资源的不同调度策略；
- 工作负载编排 - 负责在资源之间共享工作负载，如 Kubernetes 通过不同的 controller 将 Pod 调度到合适的 node 上，并且负责管理它们的生命周期；
- 服务编排 - 负责服务发现和高可用等，如 Kubernetes 中可用通过 Service 来对内暴露服务，通过 Ingress 来对外暴露服务。

#### k8s的ingress

k8s暴露服务的三种方式: LoadBlancer Service,NodePort Service,Ingress

你原来要改 Nginx 配置，然后配置各种域名对应哪个 Service，现在把这个动作抽象出来，变成一个 Ingress 对象，你可以用 yml 创建，每次不要去改 Nginx 了，直接改 yml 然后创建/更新就行了；

ingress分为ingress controller与ingress配置。ingress controller是反向代理服务器，对外通过NodePort（或者其他方式）来暴露，ingress配置是抽象出来的域名代理配置。

#### Replication Controller

1. 特性和作用：

- 通过定义 RC 实现 Pod 的创建过程及副本数量的自动控制。
- RC 里包括完成的 Pod 定义模板
- RC 通过 Label Selector 机制实现对 Pod 副本的自动控制。
- 通过改变 RC 中的 Pod 副本数量，可以实现对 Pod 的扩容或缩容功能。
- 通过改变 RC 中的镜像版本，实现 Pod 的滚动升级功能。

#### controller

- Deployment
  Deployment-->ReplicaSet-->Pod
  创建一个 Deployment 后会创建一个 ReplicaSet，然后 ReplicaSet 会创建预期数量的 pod。deployment 可以对 ReplicaSet 的版本进行管理，方便回滚。在生产上一般都是创建一个 Deployment

  无状态应用部署，比如微服务，提供多副本等功能

- StatefulSet(sts)
  有状态应用部署，比如 redis,提供稳定的存储，网络等功能
- DaemonSet
  守护型应用部署，比如日志收集组件，在每个机器都运行一份
- Job/CronJob
  定时任务部署，比如垃圾清理组件，可以在指定时间运行

- 配置文件通用 yaml 字段
  apiVersion
  kind
  metadata
  spec
- ingress
  暴露从集群外到集群内服务的 HTTP 或 HTTPS 路由。定义在 ingress 资源上的规则控制流量的路由

- namespace

```bash
    kubectl create namespace liusy-01
```

```yaml
# liusy-02-ns.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: liusy-02
```

```bash
    	kubectl create -f liusy-02-ns.yaml(创建namespace)(啥组件都可用，用yaml定义即可创建)
    	kubectl get ns(查看Namespace)
```

- k8s 的服务发现机制

  K8S 中的 Service 都有一个唯一的 Cluster IP 和唯一的名字，通过 Add-On 增值包的方式引入 DNS 系统，把服务名作为域名，程序就可以直接使用服务名来建立通信连接了。

- 外部系统访问 Service
  如果需要集群外的节点访问集群，解决方案就是使用 NodePort
  NodePort 的实现方式是在 K8S 集群中的每个 Node 上为需要外部访问的 Service 开启一个对应的 TCP 监听端口，外部系统只需要任意一个 Node 的 IP 地址+具体 NodePort 的端口即可访问服务。

volume pod 下多个容器的共享目录

deployment 是 pod 版本管理的工具 用来区分不同版本的 pod

deployment 根据 Pod 的标签关联到 Pod,是为了管理 pod 的生命周期

service 根据 Pod 的标签关联到 pod,是为了让外部访问到 pod,给 pod 做负载均衡

StatefulSet 本质上是 Deployment 的一种变体，在 v1.9 版本中已成为 GA 版本，
它为了解决有状态服务的问题，它所管理的 Pod 拥有固定的 Pod 名称，启停顺序，
在 StatefulSet 中，Pod 名字称为网络标识(hostname)，还必须要用到共享存储。

deployment 控制器关联的 Pod,Pod 的 name 和 hostname(如果不手动指定)就是 deployment 控制器的 Name

StatefulSet 控制器关联的 Pod,Pod 的 Name 和 Hostname(如果不手动指定)就是 StatefulSet 控制器的 Name + 序号

#### 存储

Local Persistent Volume
本地卷也就是 LPV 不支持动态供给的方式，延迟绑定，就是为了综合考虑所有因素再进行 POD 调度

动态供给是 PV 跟着 POD 走，而 LPV 是 POD 跟着 PV 走

PV (Persistent Volume，持久化存储卷)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata: # PV建立不要加名称空间，因为PV属于集群级别的
  name: nfs-pv001 # PV名称
  labels: # 这些labels可以不定义
    name: nfs-pv001
    storetype: nfs
spec: # 这里的spec和volumes里面的一样
  storageClassName: normal
  accessModes: # 设置访问模型
    - ReadWriteMany
    - ReadWriteOnce
    - ReadOnlyMany
  capacity: # 设置存储空间大小
    storage: 500Mi
  persistentVolumeReclaimPolicy: Retain # 回收策略
  nfs:
    path: /work/volumes/v1
    server: stroagesrv01.contoso.com
```

PVC
PVC 是用来描述希望使用什么样的或者说是满足什么条件的存储，它的全称是 Persistent Volume Claim，也就是持久化存储声明。开发人员使用这个来描述该容器需要一个什么存储

1. PV 和 PVC 中的 spec 关键字段要匹配，比如存储（storage）大小。
2. PV 和 PVC 中的 storageClassName 字段必须一致，

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc001
  namespace: default
  labels: # 这些labels可以不定义
    name: nfs-pvc001
    storetype: nfs
    capacity: 500Mi
spec:
  storageClassName: normal
  accessModes: # PVC也需要定义访问模式，不过它的模式一定是和现有PV相同或者是它的子集，否则匹配不到PV
    - ReadWriteMany
  resources: # 定义资源要求PV满足这个PVC的要求才会被匹配到
    requests:
      storage: 500Mi # 定义要求有多大空间
```

StorageClass
PV 是运维人员来创建的，开发操作 PVC，可是大规模集群中可能会有很多 PV，如果这些 PV 都需要运维手动来处理这也是一件很繁琐的事情，所以就有了动态供给概念，也就是 Dynamic Provisioning。
而我们上面的创建的 PV 都是静态供给方式，也就是 Static Provisioning。而动态供给的关键就是 StorageClass，它的作用就是创建 PV 模板

#### 常用 shell

```bash
	kubectl get nodes
	kubectl get deployments
	kubectl get pods
	kubectl describe pods
	kubectl set image deployments/kubernetes-bootcamp(deployment name) kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2 #会滚动更新
	kubectl get pods #查看新Pods的状态
	kubectl rollout status deployments/kubernetes-bootcamp # 可以通过运行滚出状态命令来确认更新
	kubectl rollout undo deployments/kubernetes-bootcamp # 回滚更新到上一个镜像版本的pod
```

### 应用发布基本流程

```bash
# 1. 部署
# 包含namespace,deployment,service的基础yaml
# 推荐的方式
kubectl apply -f xxx.yaml

# 或者
kubectl create deployment web --image=nginx --replicas=3

# 2. 升级(更新镜像)

# 方式1：直接修改yaml文件中的镜像，然后apply
kubectl apply -f xxx.yaml
# 方式2：使用命令设置deployment使用的镜像
kubectl set image deployment <DeploymentName> <ContainerName>=<Image> -n <Namespace>
kubectl set image deployment/web nginx=nginx1.17
# 使用系统编辑器打开进行编辑
kubectl edit deployment

# 3. 回滚
kubectl rollout history deployment -n test-a
# 通过命令修改deployment的镜像进行升级时，后面加--record参数，再查看历史后就会记录这条命令
kubectl set image deployment goweb-demo goweb-demo=192.168.11.247/web-demo/goweb-demo:20221229v2 -n test-a --record

kubectl get rs -n test-a

kubectl rollout history deployment -n test-a

kubectl describe $(kubectl get rs -o name -n test-a | grep "goweb-") -n test-a | grep -E "revision:|Image:"

kubectl describe $(kubectl get rs -o name -n test-a | grep "goweb-") -n test-a | grep -E "revision:|Image:|Name:|Replicas:"

kubectl rollout undo deployment goweb-demo -n test-a

kubectl describe $(kubectl get rs -o name -n test-a | grep "goweb-") -n test-a | grep -E "revision:|Replicas:"

kubectl rollout undo deployment goweb-demo --to-revision=4 -n test-a


# 4. 删除

# 如果该项目是直接编写yaml的，可这样删除（下线）
kubectl delete -f goweb-demo.yaml

# 如果该项目的命名空间、deployment、service是用命令的，那就需要手动删除下线
kubectl delete deployment goweb-demo -n test-a
kubectl delete svc goweb-demo -n test-a
kubectl delete ns test-a
```

### kubectl scale 用例

kubectl scale 用于更改 Kubernetes deployment, replica set, replication controller 和 statefulset 等对象的副本数码。当我们增加副本数时，Kubernetes 将启动新的 Pod 来扩我们的服务。降低副本数将导致 Kubernetes 优雅地终止一些 pod，从而释放集群资源。

我们可以运行 kubectl scale 来手动调整应用程序的副本数，以响应不断变化的服务容量需求。增加的流量负载可以通过增加副本数来处理，提供更多的应用程序实例来服务用户流量。当业务突发降低的时候，可以减少副本的数量。这有助于通过避免使用不需要的资源来降低成本。

### record 

1. 用户应尽量避免直接创建 Pod 和 RS，而是应该使用 Deployment 来完成 Pod 和 RS 的创建和使用。

### 学习资料

(k8s-tutorials)[https://github.com/guangzhengli/k8s-tutorials]
(k8s 中文文档)[https://www.kubernetes.org.cn/k8s]
