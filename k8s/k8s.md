# k8s知识点

## 一、概念
### 1、介绍  

Kubenetes是一个针对容器应用，进行自动部署，弹性伸缩和管理的开源系统。主要功能是生产环境中的容器编排

### 2、组成  

1个主节点master多个计算几点node

master  
* Kubectl：客户端命令行工具，作为整个K8s集群的操作入口；
* Api Server：在K8s架构中承担的是“桥梁”的角色，作为资源操作的唯一入口，它提供了认证、授权、访问控制、API注册和发现等机制。客户端与k8s群集及K8s内部组件的通信，都要通过Api Server这个组件；
* Controller-manager：负责维护群集的状态，比如故障检测、自动扩展、滚动更新等；
* Scheduler：负责资源的调度，按照预定的调度策略将pod调度到相应的node节点上；
* Etcd：担任数据中心的角色，保存了整个群集的状态

node  
* Kubelet：负责维护容器的生命周期，同时也负责Volume和网络的管理，一般运行在所有的节点，是Node节点的代理，当Scheduler确定某个node上运行pod之后，会将pod的具体信息（image，volume）等发送给该节点的kubelet，kubelet根据这些信息创建和运行容器，并向master返回运行状态。（自动修复功能：如果某个节点中的容器宕机，它会尝试重启该容器，若重启无效，则会将该pod杀死，然后重新创建一个容器）；
* Kube-proxy：Service在逻辑上代表了后端的多个pod。负责为Service提供cluster内部的服务发现和负载均衡（外界通过Service访问pod提供的服务时，Service接收到的请求后就是通过kube-proxy来转发到pod上的）；
* container-runtime：是负责管理运行容器的软件，比如docker
* Pod：是k8s集群里面最小的单位。每个pod里边可以运行一个或多个container（容器），如果一个pod中有两个container，那么container的USR（用户）、MNT（挂载点）、PID（进程号）是相互隔离的，UTS（主机名和域名）、IPC（消息队列）、NET（网络栈）是相互共享的。

## 二、安装教程
参考：https://www.cnblogs.com/xuweiweiwoaini/p/13884112.html  
方式一：通过kubeadm
```text
kubeadm、kubelet和kubectl

kuebadm init
kubeadm join
```
注意保持版本一致不然会出错

方式二：二进制安装  

|角色	|IP     |组件     |
|  ----  | ----  | ----  |  
|k8s-master	|192.168.217.100	|kube-api-server、kube-controller-manager、kube-scheduler、docker、etcd|
|k8s-node1	|192.168.217.101	|kubelet、kube-proxy、docker、etcd|
|k8s-node2	|192.168.217.102	|kubelet、kube-proxy、docker、etcd|


## 三、常用命令

kubectl

```shell script

```