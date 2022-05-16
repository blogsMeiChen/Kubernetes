# kuboard
[kuboard官网](https://kuboard.cn/install/v3/install-in-k8s.html#%E5%AE%89%E8%A3%85)

[kuboard 重置密码](https://kuboard.cn/install/v3/reset-password.html#%E9%87%8D%E7%BD%AE%E6%99%AE%E9%80%9A%E7%94%A8%E6%88%B7%E7%9A%84%E5%AF%86%E7%A0%81)

# kuboard 安装
` kubectl apply -f https://addons.kuboard.cn/kuboard/kuboard-v3-swr.yaml`

# kuboard 卸载
` kubectl delete -f https://addons.kuboard.cn/kuboard/kuboard-v3.yaml`

# 清理遗留数据
`rm -rf /usr/share/kuboard`

# 重置admin的密码
- 进入容器
`docker exec -it kuboard /bin/bash `
- 执行重置密码的命令
`kuboard-admin reset-password`

# 访问

ip:30080

用户：admin
密码：Kuboard123
