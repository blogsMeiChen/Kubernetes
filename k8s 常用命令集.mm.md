# k8s 常用命令集
## 监控 kubelet 错误
```bash
journalctl -xefu kubelet | egrep ": [F][0-9]" -B 1
```
## 自动补全工具
### kube-shell
```js
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
    # 查看端口号                           这里的kubernetes-dashboard是k8s中的某个服务名称
    kubectl get service --all-namespaces | grep kubernetes-dashboard
    kubectl get service -A
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
kubectl edit pods ng-ingress-nginx-ingress-controller-78b76bf679-ft74x
```
## 进入容器
 kubectl exec vimo-auth-server-554c8bfd5-6d9l7 -it  -- bash
 
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
    kubectl get pods -A -o wide
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
    sudo kubeadm init  --kubernetes-version=v1.22.2 --image-repository=k8s.gcr.io --ignore-preflight-errors=Swap  --apiserver-advertise-address=166.66.66.66 --upload-certs  --v=5
```
### 污点（taint）
```
污点(taint)的组成
使用kubectl taint命令可以给某个node节点设置污点，node被设置之后就和pod之间存在了一种想斥的关系，可以让node拒绝pod的调度执行，甚至将node已经存在的pod驱逐出去。

每个污点的组成如下：
`ey=value:effect`
每个污点有一个key和value作为污点的标签，其中value可以为空，effect描述污点的作用。当前taint effect支持如下三个选项：
- NoSchedule: 表示k8s将不会将Pod调度到具有该污点的pod上
- PreferNoSchedule: 表示k8s将尽量避免将Pod调度到具有该污点的node上
- NoExecute: 表示k8s将不会将Pod调式到具有该污点的Node上，同时会将Node上已经存在的Pod驱逐出去

# 定义一个hostname变量，变量值为hostname （hostname 命令是获取本地主机名称的命令）
hostname=`hostname`
# 设置污点(taint)
kubectl taint nodes ${hostname,,} node-role.kubernetes.io/master:NoSchedule-
# 去除污点(taint)
kubectl taint nodes node1 key:NoSchedule-
[kubectl taint 学习文档](https://blog.frognew.com/2018/05/taint-and-toleration.html)

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

