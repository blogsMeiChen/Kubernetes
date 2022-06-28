<center> <font face="楷体" size=10 > helm 的使用</font></center>

@(Helm实战)[学习笔记| 手册|Markdown]

---

[TOC]

### 安装Helm的两种方式
#### 二进制安装
[访问github下载自己需要的版本](https://github.com/helm/helm/releases)
```bash
cat >> install_helm.sh<<-'EOF'
wget https://get.helm.sh/helm-v3.7.2-linux-amd64.tar.gz
tar -xf helm-v3.7.2-linux-amd64.tar.gz
cd linux-amd64
sudo cp helm /usr/local/bin
helm version
EOF
bash -ex install_helm.sh
```
####  自动脚本下载
```bash
curl -L https://git.io/get_helm.sh | bash
```

### 开始使用

> 查看版本时会报警告信息

![image](https://user-images.githubusercontent.com/65467296/119291478-14c21980-bc81-11eb-99c1-9b09c9cb0027.png)

> 解决警告信息
`chmod go-r ~/.kube/config`
```bash
#先添加常用的chart源
helm repo add stable https://burdenbear.github.io/kube-charts-mirror/
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com  
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add aliyuncs https://apphub.aliyuncs.com

#查看chart列表
[root@master nginx]# helm repo list
# 更新charts列表
helm repo update
```
#### 查询
```bash
helm search repo smiling-penguin
```

#### 安装
```bash
helm install smiling-penguin
```
#### 下载 
```bash
helm fetch stable/mysql
```
#### 删除
```bash
helm delete  smiling-penguin
```

#### 打包
```bash
helm package .
```
#### 卸载
```bash
helm uninstall  smiling-penguin
```
#### 状态
```bash
helm status smiling-penguin
```
#### 搭建本地仓库
```bash
github 二进制安装包
[helm-push](https://github.com/chartmuseum/helm-push)

helm plugin install https://github.com/chartmuseum/helm-push.git

```

```bash
# 创建
helm create acme
# 验证
helm lint
# 安装
helm install --dry-run \
--name acme-stocks ./acme
---
helm install --dry-run --debug ./mychart
# 查询
helm ls -all acme-stocks
# 删除
helm del --purge acme-stocks
# 模板
helm template ./mychart | kubectl apply --dry-run -f -
# 升级
helm  upgrade 
# 回滚
helm  rollback
# 历史
helm history 
```

### helm puls 插件推荐 
##### 文本对比插件 diff 必装

```bash
helm plugin install https://github.com/databus23/helm-diff
```



### Helm3 自定义chart编写

```bash
# 创建 helm create myapp
# 目录结构介绍
m@m:~$ tree myapp/
myapp/
├── charts	# 目录可以包含其他的chart（称之为 子chart）。
├── Chart.yaml	# 文件包含了该chart的描述。你可以从模板中访问它。
├── templates  # 目录包括模板文件。当Helm评估chart时，会通过模板渲染引擎将所有文件发送到templates/目录中。然后手机模板的结果并发送给kubernetes。
│   ├── deployment.yaml	# 创建kubernetes工作负载的基本清单
│   ├── _helpers.tpl	# 里面的内容是定义模板用的，所有模板都可以在这里定义，然后再任何yaml文件当中都可以调用这个文件下的模板
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt	# chart的"帮助文档"。这会在你的用户执行 helm install 时展示给他们。
│   ├── serviceaccount.yaml	
│   ├── service.yaml	# 为你的工作负载创建一个service终端基本清单
│   └── tests
│       └── test-connection.yaml
└── values.yaml # 文件也导入到了模板。 这个文件包含了chart的默认值。这些值会在用户执行 helm install 或者 helm upgrade时被覆盖
```

## 暴漏端口号
```bash
helm install vimo ./vimo --set=vimo-frontend.vimo-front.service.nodePort=32000
```

