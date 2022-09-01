# k8s知识点

---

参考：https://zhuanlan.zhihu.com/p/365759073  

---

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
### 2.1.linux上安装k8s
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

### 2.2.mac上安装k8s
参考：https://www.jianshu.com/p/a6abdc6f76e1


## 三、常用命令

kubectl

```shell
# 查看nodes
kubectl get nodes

# 查看命名空间
kubectl get ns

# 查看pod
kubectl get pods -n kube-system

# 查看pod更多信息
kubectl get pod etcd-docker-desktop -n kube-system -o wide

# 查看pod信息以yaml格式
kubectl get pod etcd-docker-desktop -n kube-system -o yaml

# 创建service和rc
kubectl create -f my-service.yaml -f my-rc.yaml

# 创建命名空间
kubectl create ns test

# 创建pod
kubectl create -f nginx.yaml 
# 修改pod
kubectl apply -f nginx.yaml
```

## 四、基础组件
### 4.1 pod
+ pause  
pause进程是pod中所有容器的父进程,主要是负责僵尸进程的回收管理
通过pause容器可以使同一个pod里面的多个容器共享存储、网络、pid、
ipc等

### 4.2 组件
pod可以简写为po  
deployment可以简写为deploy  

+ rc与rs  
  控制副本数量的，一般不使用
+ deployment  
  无状态：可以随便重启，也可以管理rc

```shell
# 手动创建deployment
kubectl create deployment nginx --image=nginx:1.15.2
kubectl get deployment
kubectl delete deployment nginx
# 查看滚动状态
kubectl rollout status deploy nginx
# 查看版本记录用于回滚时使用
kubectl rollout history deploy nginx
# 回滚到上一个版本
kubectl rollout undo deploy nginx
# 查看某个版本的具体信息
kubectl rollout history deploy nginx --revision=5
# 回滚到指定版本
kubectl rollout undo deploy nginx --to-revision=5
# 扩容
kubectl scale --replicas=3 deploy nginx
# 暂停，用于多次修改生效
kubectl rollout pause deploy nginx
# 修改deploy参数, --record用作历史记录
kubectl set image deploy nginx=nginx:1.15.3 --record
```

+ statefulset  
  有状态：部署redis、mysql  
+ daemonset  
  每个节点上都启动一个组件，比如可以管理网络

+ label
  对k8s中各种资源进行分类、分组  

```shell
# 给node创建label
kubectl label node k8s-node2 region=subnet2
# 过滤label
kubectl get node -l region=subnet2
# 查看pod的label
kubectl get po --show-labels
# 查看pod的所有label
kubectl get po -A --show-labels
# 过滤label
kubectl get po -A -l app=busybox
# 为pod新建label
kubectl label po busybox app=busybox -n kube-system
# 为pod删除label， 加-
kubectl label po busybox app- -n kube-system
# 修改label, --overwrite
kubectl label po busybox app=busybox -n kube-system --overwrite

```

+ selector
  通过一个过滤语法查找到对应标签的资源

+ service
  服务间的通讯，即：东西通讯，通过svc
  用户通过外网访问，即：南北通讯， 通过ingress

  创建svc后同时也会创建出一个endpoint(ep)，该ep会记录pod的ip
  svc的yaml文件中会通过selector去选择响应的pod

```shell
# 查看ep
kubectl get ep nginx-svc
```

svc使用示例
```text
- 通过svc反向代理外部服务
yaml文件中不指定selector,自己创建ep,ep写要代理的服务ip
```

svc类型
```text
- clusterIP  
  在集群内部使用，默认
- externalName  
  通过返回定义的CNAME别名
- nodePort  
  在所有安装了kube-proxy的节点打开一个端口，此端口可以
  代理至后端pod,然后集群外部可以使用节点的ip地址和nodePort的
  端口号访问到集群的pod服务，端口号范围30000-32767
- loadBalancer  
  通过云服务商公布服务
```

+ configMap
+ secret
  imagepullsecret 拉取私有镜像时进行配置用户名和密码
+ volumes  
  - emptyDir 参考：https://kubernetes.io/docs/concepts/storage/volumes/
  - hostPath
  - nfs
+ pv  
  状态：   
    - available：空闲的pv,没有被任何pvc绑定
    - bound: 已经被pvc绑定
    - released: pvc被删除，但是资源未被重新使用
    - failed: 自动回收失败
+ pvc  
  pvc绑定pv时需注意storageClassName与accessmodes必须一致  

+ cronJob
  * * * * * 分时日月周

+ taint 污点
  tolerations 容忍

+ initContainer
  容器初始化时进行的一些操作

+ Affinity 亲和力
  