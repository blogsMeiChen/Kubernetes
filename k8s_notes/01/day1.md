## kubernetes 的设计架构
在kubernetes集群中，有Master和Node这两种角色：
  - Master 管理Node
    Master主要负责整个集群的管理控制，相当于整个kubernetes集群的首脑。
    它用于监控，编排，调度集群中的各个工作节点，通常Master会占用一台独立的服务器，基于高可用原因，也有可能是多台
    
  - Node 管理容器
    Node则是kubernetes集群中的各个工作节点，Node由Master管理，提供运行容器所需要的各个环境，对容器进行实际的控制，而这些容器会提供实际的应用服务。
    
    


