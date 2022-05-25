# Kubernetes 学习笔记

---

[TOC]

## kubernetes 的设计架构
> 在kubernetes集群中，有Master和Node这两种角色：
  - Master 管理Node
    Master主要负责整个集群的管理控制，相当于整个kubernetes集群的首脑。
    它用于监控，编排，调度集群中的各个工作节点，通常Master会占用一台独立的服务器，基于高可用原因，也有可能是多台
   
  - Node 管理容器
    Node则是kubernetes集群中的各个工作节点，Node由Master管理，提供运行容器所需要的各个环境，对容器进行实际的控制，而这些容器会提供实际的应用服务。
      
## Kubernetes 存储与配置

> 在 Kubermetes 中定义的存储卷主要分为 4 种类型

- 本地存储卷：主要用于 pod 容器之间的数据共享，或 pod 与 Node 中的数据存储和共享。
- 网络存储卷：主要用户多个 pod 之间或多个 Node 之间的数据存储和共享。
- 持久存储卷：基于网络存储卷，用户无需关心存储卷所使用的存储系统，只需要自定义具体需要消费多少资源即可，可将 pod 与具体的存储系统解藕。
- 配置存储卷：主要用于向各个 pod 注入配置信息。

### 本地存储卷

> 本地存储卷卷有 emptyDir 和 hostPath 两种，它会直接使用本机的文件系统,用于 pod 中容器之间的数据共享，或 pod 与 Node 中的数据和共享。

- emptyDir：顾名思义，emptyDir 是指一个纯净的空目录,这个目录映射到主机的一个临时目录下，pod 中的容器都可以读写这个目录，其生命周期和 pod 完全一致，如果 pod 销毁，那么存储卷也会同时销毁，
- emptyDir：主要用于存放和共享 pod 的不同容器之间在运行过程中产生的文件。

----
## Pod 的基本操作
- 创建Pod
```bash
tee >> examplpod.yml <<'EOF'

apiVersion: v1  # 表示使用的API版本。v1表示使用KubernetesAPI的稳定版本。
kind: 表示要创建的资源对象，这里使用关键字Pod
metadata: 表示该资源对象的元数据。一个资源对象可拥有多个元数据，
  name: 其中一项是name，它表示当前资源的名称。
spec: 表示该资源对象的具体设置。其中containers表示容器的集合，这里只是设置了一个容器，该容器的属性如下。
   name: 要创建的容器名称。
   image: 容器的镜像地址。
   imagePullPolicy: 镜像的下载策略，支持3种imagePullPolicy,如下所示。
    # Always: 不管镜像是否存在都会进行一次拉取。
    # Never: 不管镜像是否存在都不会镜像拉取。
    # IfNotPresent: 只有镜像不存在时，才会进行拉取。
   command: 容器的启动命令列表(不配置的话，使用镜像内部的命令)
   args: 启动参数列表(在本例中是输出文字"Hello Kubernetes!" 并休眠3200s)
   EOF
   ```
## kubernetes pod 的几种状态
-  Evicted
-  ErrImageNeverPull 
-  Running
-  
