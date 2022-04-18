[文档引用出处 ](https://mp.weixin.qq.com/s/wPo0kpeUDu8amGquIdXUCQ)

helm repo add jenkinsci https://charts.jenkins.io

helm repo update
# 我习惯把CHART下载到本地，方便管理
helm pull jenkinsci/jenkins
# 这里有一步解压的过程，然后进入Jenkins目录进行部署
# 部署
kubectl create ns devops

helm install jenkins -n devops .
