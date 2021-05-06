
# kubernetes学习笔记

## k8s命令自动补全
- CentOS
-
  ```bash
  yum -y install bash-completion
  locate bash_completion
  source /usr/share/bash-completion/bash_completion
  source <(kubectl completion bash)
  echo "source <(kubectl completion bash)" >> ~/.bashrc
  ```
 - Ubuntu
 -
    ```bash
    apt-get install bash-completion
    locate bash_completion
    source /usr/share/bash-completion/bash_completion
    source <(kubectl completion bash)
    echo "source <(kubectl completion bash)" >> ~/.bashrc
    ```

## 创建
## 查询
### pods
  -
    ```bash
    kubectl get pods 
    kubectl get pods -A
    ```
## 删除
- 
  ```bash
  先查询要删除的pod名称
  kubectl get pods -A
  再进行删除
  kubectl delete -n namespace+name
  ```
## 日志

-
  ```bash
  先查询pod名称
  kubectl get pods -A
  再浏览日志
  kubectl logs -n namespce+name
  
  ```

## 网络

## 存储

# Kubernetes 学习笔记

## 网络

### 端口

- 查看namespace暴露的端口  

  # 查看网络的信息状态
  kubectl get pods -o wide
  kubectl get pod --namespace=kube-system -o wide
  
- 查看所有服务暴露的端口

  # 查看NodePort 像外暴露的端口
  netstat -nlpt  | grep -Po ':::\K\d+(?=.+kube-proxy)' | sort -rn | xargs -n8
  
  

### 网络信息

# 查看网络的信息状态
kubectl get pods -o wide
#  查看单个namespace 网络信息
kubectl get pod --namespace=kube-system -o wide


## 查询

### node

kubectl get node


### pods

kubectl get pods 
# 查看所有的pods 节点
kubectl get pods  -A


### describe

- 查询所有pods详情

  # 查询所有pods 详情
  kubectl describe pods
  # 查询单个pods 详情
  kubectl describe pods -n  namespace   name
  #示例
  先使用 kubectl get pods -A 查询到所有pod 需要查询那个单独的pod 就把 namespace 与 name 输入到 pods -n 后面
  kubectl describe pods -n 28793185851   train-1-4
  

### 查询配置文件

- 查询namespace的配置文件

  # 查找 namespace 所有的yaml配置信息
  kubectl get pods -n kube-system  -o yaml
  # 使用grep 查找单个字符内容
  kubectl get pods -n kube-system  -o yaml | grep hostIP:
  

### rc,server 

# 查看rc和service列表
kubectl get rc,service


### 格式化输出

# 格式化输出，使其仅仅包含容器镜像名称，这将递归解析返回的json对象中的image字段。
kubectl get pods -o jsonpath={..image}


## 查询组建状态

kubectl get componentstatuses


## 删除

### pods

# 先使用get方法查找到删除的pods 节点名称
kubectl   get pods -A
# 再使用delete方法删除pods节点
kubectl delete pods -n  namespace   name
# 删除所有的pods节点
kubectl delete pods --all
# 删除单个namespace
kubectl delete pods  namespace



### 删除所有节点信息

kubeadm reset


### 有yaml文件时删除方法

kubectl delete -f 要删除的服务文件.yaml


## 内存

### 查看pod节点资源

# 查看资源
kubectl top pods
kubectl top pods -A


## 日志

### 日志转存储

# 集群日志转存储
kubectl cluster-info dump --all-namespaces --output-directory "cluster-info-$(date +%s)"


## 更新yaml文件

### replace更新

kubectl replace  需要更新的文件路径.yaml


## 创建容器

### apply

kubectl  apply -f  要创建的文件.yaml


# k8s 常用命令集
## 自动补全工具
### kube-shell
```bash
    # 必须系统安装有python2.7版本才可安装
    pip install kube-shell
```
### tab补全
```bash
    # 自动补齐需要依赖工具bash-complete
    # Ubuntu 配置kubernetes命令自动补全
    sudo apt-get install -y bash-completion
    # CntOS 配置kubernetts命令自动补全
    yum -y install bash-completion
    locate bash_completion
    source /usr/share/bash-completion/bash_completion
    source <(kubectl completion bash)
    echo "source <(kubectl completion bash)" >> ~/.bash_profile
```
### 设置别名
```bash
    #添加kubectl的k别名
    vim   ~/.bashrc
    alias k='kubectl'
    #tab命令只在使用完整的kubectl 命令起作用，使用别名k 时不起作用，修补：
    source <( kubectl completion bash | sed 's/kubectl/k/g' )  #写入 .bashr
```
## 查询
### pods
```bash
    # 1、查询所有pods
    # 方法一
    kubectl get pods -A
    # 方法二
    kubectl get pods --all-namespaces
    # 查询所有pods列表
    kubectl get pods
    kubectl get namespaces
```
### logs
```bash
    # 先使用kubectl get pods -A 找到需要查询的日志的pods NAMESPACE 与  NAME 对应的字段pods
    kubectl logs -n  NAMESPACE    NAME
    # 实时打印100行日志信息
    kubectl logs -f --tail 100 NAME
    # 集群日志转存储
    kubectl cluster-info dump --all-namespaces --output-directory "cluster-info-$(date +%s)"
```
### describe
```bash
    # 查询单个NAMESPACE    NAME的配置详情信息
    kubectl describe pods -n NAMESPACE    NAME
    # 查询NAME的配置详情信息
    kubectl describe pods  NAME
    # 查询有所的pods
    kubectl describe pods 
```
### 查询端口号
```bash
    # 查看端口号                             这里是服务中的namespace名称
    kubectl get service --all-namespaces | grep kubernetes-dashboard
    # 查询服务暴漏的端口
    kubectl get svc
```

### 查询镜像
```bash
    # 格式化输出，使其仅仅包含容器镜像名称，这将递归解析返回的json对象中的image字段。
    kubectl get pods -o jsonpath={..image}
```

### 查询组件状态
```bash
    # 查询组件状态
    kubectl get componentsta
```
### 查看节点
```bash
    kubectl get node
```
### 查询集群信息
```bash
    kubectl cluster-info
    kubectl cluster-info dump
```
### 查询rc,service
```bash
    # 查看集群信息
    kubectl get services 
    # 查看rc和service列表
    kubectl get rc,service
```
### 资源查询
```bash
    # 查询pods资源，可看到CPU与内存的使用率
    kubectl top pods
    # 查询所有pods资源
    kubectl top pods -A
    kubectl top pods --all-namespaces
```
### 查看distype=ssd标签
```bash
    # 证你选择节点有distype=ssd标签
    kubectl get nodes --show-labels
```
## 创建
### apply
```bash
    # 将pod.yaml 创建到pod中
    kubectl apply -f pods.yaml
    kubectl create -f pods.yaml
```
## 编辑
```bash

```
## 更新
```bash
    # 更新
    kubectl replace /path/to/yourNewYaml.yaml
```
## 删除
```bash
    # 删除 NAME
    kubectl delete pods sminsight-dev-console-5f45bb89cb-74m8k
    # 删除单个pods
    kubectl delete pods -n NAMESPACES NAME
    # 删除所有的pods
    kubectl delete pods --all
    # 通过yaml文件删除pods
    kubectl delete -f  pod.yaml
	# 强制删除pods
	kubectl delete pods pods name --grace-period=0 --force
```
## 网络
```bash
    # 查看pods的网络状态
    kubectl get pods -o wide
    # 查看单个name的网络状态
    kubectl get pod --namespace=kube-system -o wide
```
## 回滚
```bash

```
## kubeadm
### 清除集群所有节点pods信息
```bash
    kubeadm reset -f
```
### 初始化
```bash
    kubeadm init 
```
### 查询配置镜像
```bash
    kubeadm config images list
```
## 其他未归类
```bash
    kubectl get deployments -A
    kubectl get deployments --all-namespaces
    # 查看 kube-system 名称空间的 Deployment
    kubectl get deployments -n kube-system
    # 在名称空间里
    kubectl api-resources --namespaced=true
    # 不在名称空间里
    kubectl api-resources --namespaced=false

    # 验证这个pod是否运行在选择的机器上
    kubectl get pods --output=wide
    kubectl get service --namespace=kube-system
    kubectl get deployment --namespace=kube-system

```

