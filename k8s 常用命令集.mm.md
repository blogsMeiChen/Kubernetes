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
    yum -y install bash-completion mlocate
    updatedb
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

# kubectl get pods -A 输出的几个字段各自代表的意思

![image](https://user-images.githubusercontent.com/65467296/164166808-bdae816b-136a-4c67-be55-bc8fc9339a3d.png)

```bash
命令空间             名称                                              副本     状态       重新启动   时间
NAMESPACE            NAME                                             READY   STATUS      RESTARTS   AGE
参数讲解：

    NAMESPACE：命令空间, 属于当前命令空间的所有pod都会显示出来
    NAME：名称, 列出了集群中 Deployment 的名称。
    READY：副本, 显示应用程序的可用的“副本”数。显示的模式是“就绪个数/期望个数”。
    STATUS：状态, 显示应用当前的运行状态
    RESTARTS：重新启动, 显示当前应用的重启次数
    AGE：运行时间, 显示应用程序运行的时间。
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
    # 输出集群中所有正在使用中容器的镜像名称
    kubectl get pods --all-namespaces -o jsonpath="{..image}" |tr -s '[[:space:]]' '\n' |sort |uniq -c
    # 输出某个命令空间正在使用中的容器镜像名称
    kubectl get pods -n vimo  -o jsonpath="{..image}" |tr -s '[[:space:]]' '\n' |sort |uniq -c
    # 参数详解：
        使用 kubectl get pods --all-namespaces 获取所有命名空间下的所有 Pod
        使用 -o jsonpath={..image} 来格式化输出，以仅包含容器镜像名称。 这将以递归方式从返回的 json 中解析出 image 字段。
        参阅 jsonpath 说明 获取更多关于如何使用 jsonpath 的信息。
        使用标准化工具来格式化输出：tr, sort, uniq
        使用 tr 以用换行符替换空格
        使用 sort 来对结果进行排序
        使用 uniq 来聚合镜像计数
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
## 存储PV
```bash
kubectl get pv
kubectl get pvc
kubectl get pvc -n vimo
```
## 强制删除PV, PVC
```bash
kubectl patch pv vimo-data  -p '{"metadata":{"finalizers":null}}'
kubectl patch pvc vimo-data  -p '{"metadata":{"finalizers":null}}'
```
## 创建

```bash
# 将pod.yaml 创建到pod中
# apply
kubectl apply -f pods.yaml
# create
kubectl create -f pods.yaml
```
## 编辑
```bash
kubectl edit pods ng-ingress-nginx-ingress-controller-78b76bf679-ft74x
```
## 进入容器
```bash
kubectl exec vimo-auth-server-554c8bfd5-6d9l7 -it  -- bash
kubectl exec -ti vimo-mysql-7c597cf94d-pwbsf -n vimo bash
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
    # 没有yaml文件 强制删除pods
    kubectl delete pods  name --grace-period=0 --force
    # 批量删除
    kubectl get pods -A | grep default | awk '{print $1" " $2  " --force --grace-period=0 "}' |xargs  kubectl delete pods -n
    
    # 命令   参数                        命令空间    名称
    kubectl delete deployments.apps -n  NAMESPACE   NAME
    # 示例单个删除：
    kubectl delete deployments.apps -n default vimo-stable-mysql
    示例指定指定命令空间永久删除删除：
    kubectl get deployments.apps  | awk '{print $1}'| xargs kubectl delete deployments.apps
    
   
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
    sudo kubeadm init  --kubernetes-version=v1.18.5 --image-repository=k8s.gcr.io --ignore-preflight-errors=Swap  --apiserver-advertise-address=166.66.66.66 --upload-certs  --v=5
```
初始化失败查看kubelet信息

`journalctl -xeu kubelet`

`systemctl status kubelet`

`docker ps -a | grep kube | grep -v pause`


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


# 设置污点(taint)
kubectl taint nodes `whoami` node-role.kubernetes.io/master:NoSchedule-
kubectl taint nodes --all node-role.kubernetes.io/master-

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
    kubectl config view --minify --raw

```
### 证书到期解决方法

![image](https://user-images.githubusercontent.com/65467296/190980069-b3a6d92d-feda-453f-b485-f6885cf679d1.png)

```bash
# 查看证书有效期
sudo kubeadm alpha certs check-expiration
# 更新全部证书
kubeadm alpha certs renew all
# 用户权限更新
cp-i /etc/kubernetes/admin.conf SHOME/.kube/config
# 重启kubelet apiserver etcd scheduler
docker ps | grep -E 'k8s_kube-apiserver | k8s_kube-controller-manager | k8s_kube-scheduler | k8s_etcd_etcd' awk-F '{print $1}'| xargs docker restart
```
[自动更新证书](https://kubernetes.io/zh/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#%E8%87%AA%E5%8A%A8%E6%9B%B4%E6%96%B0%E8%AF%81%E4%B9%A6)

### 集群中列出所有运行的容器的镜像
[kubernetes官网](https://kubernetes.io/zh/docs/tasks/access-application-cluster/list-all-running-container-images/)

### Kubelet各个端口的作用
+ kubelet 默认监听四个端口，分别为： 10250 、10255、10248、4194
    + 10250：kubelet API：kubelet server 与 apiserver 通信的端口，定期请求 apiserver 获取自己所应当处理的任务，通过该端口可以访问获取 node 资源以及状态。kubectl查看pod的日志和cmd命令，都是通过kubelet端口10250访问，如果本地没有开启10250端口的话：
