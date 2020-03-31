---
title: K8s入门
tags:
  - k8s                   
categories:
- k8s 
top: true
---

# docker

![](/images/EB6EDD19-F7E7-445B-B35C-0B570E411FD5.png)

![](/i/BA64038A-100C-4BEA-9731-8F44FE00DED6.png)



 * 它被宿主机操作系统隔离了，它看不到宿主机上的其他进程，以为自己就是pid为1的进程

 * 它被宿主机操作系统限制了硬件资源，它能使用的cpu、内存等硬件资源只是宿主机的一部分

 * 在被宿主机操作系统限制了存储空间，让这个进程认为宿主机的某个目录就是系统根目录



`Namespace做隔离，Cgroups做限制，rootfs做文件系统`



`容器是操作系统层级的虚拟化，而虚拟机是硬件层级的虚拟化。—本质区别`





### 为什么用K8s？



 - **自愈**重新启动失败的容器，在节点不可用时，替换和重新调度节点上的容器，对用户定义的健康检查不响应的容器会被中止，并且在容器准备好服务之前不会把其向客户端广播。

 - **弹性伸缩**通过监控容器的cpu的负载值,如果这个平均高于80%,增加容器的数量,如果这个平均低于10%,减少容器的数量

 - **服务的自动发现和负载均衡** 不需要修改您的应用程序来使用不熟悉的服务发现机制，Kubernetes 为容器提供了自己的 IP 地址和一组容器的单个 DNS 名称，并可以在它们之间进行负载均衡。

 - **滚动升级和一键回滚** Kubernetes 逐渐部署对应用程序或其配置的更改，同时监视应用程序运行状况，以确保它不会同时终止所有实例。 如果出现问题，Kubernetes会为您恢复更改，利用日益增长的部署解决方案的生态系统。





**容器的本质是进程，Kuberbetes的本质是操作系统。**



## 术语

* 节点：Kubernetes集群中的一台物理机或者虚拟机。

* 集群：位于Internet防火墙后的节点，这是kubernetes管理的主要计算资源。

* 边界路由器：为集群强制执行防火墙策略的路由器。 这可能是由云提供商或物理硬件管理的网关。

* 集群网络：一组逻辑或物理链接，可根据Kubernetes [网络模型](https://kubernetes.io/docs/admin/networking/) 实现群集内的通信。 集群网络的实现包括Overlay模型的  [flannel](https://github.com/coreos/flannel#flannel)  和基于SDN的 [OVS](https://kubernetes.io/docs/admin/ovs-networking/) 。

* 服务：使用标签选择器标识一组pod成为的Kubernetes [服务](https://kubernetes.io/docs/user-guide/services/) 。 除非另有说明，否则服务假定在集群网络内仅可通过虚拟IP访问。



#### k8s集群

 1. 基本结构

 [Kubernetes集群组成](https://lahmxu.github.io/2019/12/19/K8s基本组成/)

 2. Pod

 [K8s Pod](https://lahmxu.github.io/2019/12/19/K8s%20Pod/)

 3. Controller

 [K8s Controller](https://lahmxu.github.io/2019/12/19/K8s%20Controller/)

 4. Service 

 [K8s Service](https://lahmxu.github.io/2019/12/19/K8s%20Service/)

 5. PV&PVC

 [K8s PV&PVC](https://blog.csdn.net/xts_huangxin/article/details/51494472) 

 6. ConfigMap&Secret

 [ConfigMapSecret](https://www.kubernetes.org.cn/3400.html)

  - ConfigMap：存储配置信息

  - Secret ：存储秘钥和密码之类的信息

 

### 什么是声明式API？声明式API的使用

**为什么叫“声明”API ？**

 1. 常用命令

![](/images/bVbrFZN.png)

参考链接：[Kubectl常用命令详解 - Kubernetes,Istio点滴 - SegmentFault 思否](https://segmentfault.com/a/1190000018950372)

 2. kubectl create/replace和kubectl apply有什么不同？

  - 命令式配置文件操作，类似于Docker Swarm

  - 声明式API

     **不同点**

  - 命令式API对象是使用新的YAML文件中的API对象替换原有的API对象，而声明式API执行了对原有API对象的PATCH操作

  - 声明式API一次可以处理多个写操作，具备merge的能力

 3. 滚动更新 

```yaml

#新建yaml文件

cat > nginx-deployment.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
EOF



#创建Pod

kubectl apply -f nginx-deployment.yaml --record

#查看Pod创建状态

kubectl get pod

#用edit命令修改yaml文件

kubectl edit deployment nginx-deployment

#使用rollout查看滚动更新过程

kubectl rollout status deployment nginx-deployment

#查看详细更新过程

kubectl describe deployment nginx-deployment

#查看更新的replicaSet

kubectl get rs



#更改成错误的镜像1.7.9 --> 1.91

#查看当前rs pod状态



#中止滚动更新

kubectl rollout pause deployment nginx-deployment

#恢复滚动更新

kubectl rollout resume deployment nginx-deployment



#回退

#回退上一个版本

kubectl rollout undo deployment nginx-deployment

#回退指定版本

kubectl rollout history deployment nginx-deployment

kubectl rollout undo deployment/nginx-deployment --to-revision=2

```



### 三种部署方式

 1. [kubeadm安装](https://lahmxu.github.io/2019/12/19/K8s%20deploy/)

 2. [minikube安装]() 

 3. [microK8s安装]()



 



 
