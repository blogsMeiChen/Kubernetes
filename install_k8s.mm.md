# kubeadm init 

---

[TOC]

修改kubelet配置（修改时如果没有该文件就创建该文件）

`vim /etc/sysconfig/kubelet `

## 添加内容如下

`KUBELET_EXTRA_ARGS="--fail-swap-on=false"`

## 解决国内无法拉取k8s基础组件问题

# 解决方法一

#### 先查看k8s需要的基础组件镜像名称

```bash
m@m:~$ sudo kubeadm config images list

k8s.gcr.io/kube-apiserver:v1.18.20
k8s.gcr.io/kube-controller-manager:v1.18.20
k8s.gcr.io/kube-scheduler:v1.18.20
k8s.gcr.io/kube-proxy:v1.18.20
k8s.gcr.io/pause:3.2
k8s.gcr.io/etcd:3.4.3-0
k8s.gcr.io/coredns:1.6.7
```

### 生成配置文件

```bas
touch kubeadm-config.yaml
sudo kubeadm config print init-defaults  > kubeadm-config.yaml
```

参数详解：
- init-defaults: 显示init默认的初始化文件并打印到kubeadm-config.yaml文件中，这样就拿到了kubeadm默认的初始化模板


### 生成的配置文件中需要修改一下两项参数

```bash
m@m:~$ grep 'imageRepository\|kubernetesVersion\|advertiseAddress' kubeadm-config.yaml
advertiseAddress: 1.2.3.4   (修改成成0.0.0.0)
imageRepository: k8s.gcr.io (这里修改成国内k8s镜像仓库)
kubernetesVersion: v1.18.0 （这里是自己当前安装k8s版本）
```


### 修改之后的参数

```bash
m@m:~$ grep 'imageRepository\|kubernetesVersion\|advertiseAddress' kubeadm-config.yaml
advertiseAddress: 0.0.0.0
imageRepository: registry.aliyuncs.com/google_containers
kubernetesVersion: v1.18.5
```

### 再次查看kubeadm config 所需要的镜像

```bash
m@m:~$ sudo kubeadm config images list --config kubeadm-config.yaml

registry.aliyuncs.com/google_containers/kube-apiserver:v1.18.5advertiseAddress
registry.aliyuncs.com/google_containers/kube-controller-manager:v1.18.5
registry.aliyuncs.com/google_containers/kube-scheduler:v1.18.5
registry.aliyuncs.com/google_containers/kube-proxy:v1.18.5
registry.aliyuncs.com/google_containers/pause:3.2
registry.aliyuncs.com/google_containers/etcd:3.4.3-0
registry.aliyuncs.com/google_containers/coredns:1.6.7
```

### 拉取镜像

`sudo kubeadm config images pull --config kubeadm-config.yaml`

### 初始化

`sudo kubeadm  init --config kubeadm-config.yaml`

### 初始化报错

```bash
m@m:~$ sudo kubeadm init --config kubeadm-config.yaml
W0721 10:39:10.003330    7325 configset.go:202] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
[init] Using Kubernetes version: v1.18.5
[preflight] Running pre-flight checks
        [WARNING SystemVerification]: this Docker version is not on the list of validated versions: 20.10.7. Latest validated version: 19.03
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR Swap]: running with swap on is not supported. Please disable swap
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`（检查到swap交换分区未未关闭 提示添加--ignore-preflight-errors=Swap 方可解决）
To see the stack trace of this error execute with --v=5 or higher
```



## 解决方法

`sudo kubeadm init --config kubeadm-config.yaml  --ignore-preflight-errors=Swap`

### 报错信息

```bash
[kubelet-check] The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get http://localhost:10248/healthz: dial tcp [::1]:10248: connect: connection refused.
解决方法
sudo find / -name 10-kubeadm.conf
```




# 解决方法二

### 使用阿里云镜像

```bash
运行kubeadm init时加上阿里云镜像的参数--image-repository=registry.aliyuncs.com/google_containers，如下：（版本改为自己需要的）

sudo kubeadm init --image-repository=registry.aliyuncs.com/google_containers  --kubernetes-version=v1.18.5 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/16 --apiserver-advertise-address=166.66.66.66


```


### 开启ipvs

```bash
m@m:~$ kubectl edit cm kube-proxy -n=kube-system
修改mode: ipvs
```

## 安装网络组件

```bash
wget https://docs.projectcalico.org/v3.14/manifests/calico.yaml
kubectl apply -f calico.yaml
```

## 检查组件状态

`kubectl get cs`

## worker节点加入集群

```bash
kubeadm join 10.0.0.9:6443 --token abcdef.0123456789abcdef \
--discovery-token-ca-cert-hash sha256:b6d4666e4d407f611265541434bb796ff57c27cd706920ad58ba847b8a7fd109
```

## 如果join的token之前没有记住，没关系，在master重新生成一下

`sudo kubeadm token create --print-join-command`

`sudo kubeadm init --image-repository=k8s.gcr.io  --kubernetes-version=v1.18.5  --apiserver-advertise-address=166.66.66.66 --ignore-preflight-errors=Swap --upload-certs`
