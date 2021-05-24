## helm 的使用

### 手动安装helm
[访问github下载自己需要的版本](https://github.com/helm/helm/releases)
```bash
cat >> install_helm.sh<<-'EOF'
wget https://get.helm.sh/helm-v3.5.2-linux-amd64.tar.gz
tar-xf helm-v3.5.2-linux-amd64.tar.gz
cd linux-amd64
sudo cp helm /usr/local/bin
helm version
EOF
bash -ex install_helm.sh
```
### 自动脚本下载
```bash
curl -L https://git.io/get_helm.sh | bash
```

### 开始使用
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
### 查询软件包
```bash
helm search repo smiling-penguin
```
### 安装
```bash
helm install smiling-penguin
```
### 下载 
```js
helm fetch stable/mysql
```
### 删除
```bash
helm delete  smiling-penguin
```

### 打包
```bash
helm package .
```
### 卸载
```bash
helm uninstall  smiling-penguin
```
### 查看服务状态
```bash
helm status smiling-penguin
```
