---
title: K8s基本组成
tags:
  - k8s                   
categories:
- k8s 
---



Kubernetes（k8s）是自动化容器操作的开源平台，这些操作包括部署，调度和节点集群间扩展。如果你曾经用过Docker容器技术部署容器，那么可以将Docker看成Kubernetes内部使用的低级别组件。Kubernetes不仅仅支持Docker，还支持Rocket，这是另一种容器技术。

![](/images/1BEE79B7-7704-4FAE-BB04-1572B84A6D6E.png)

### Pod
[Pod]（上图绿色方框）安排在节点上，包含一组容器和卷。同一个Pod里的容器共享同一个网络命名空间，可以使用localhost互相通信。Pod是短暂的，不是持续性实体。

### Label

Pod的标签，一个Label是attach到Pod的一对键/值对，用来传递用户定义的属性。

### Replication Controller 

Replication Controller确保任意时间都有指定数量的Pod“副本”在运行。如果为某个Pod创建了Replication Controller并且指定3个副本，它会创建3个Pod，并且持续监控它们。

### Service

定义一系列Pod以及访问这些Pod的策略的一层**抽象**。Service通过Label找到Pod组。因为Service是抽象的，所以在图表里通常看不到它们的存在，这也就让这一概念更难以理解。

### Node

节点（上图橘色方框）是物理或者虚拟机器，作为Kubernetes worker，通常称为Minion。每个节点都运行如下Kubernetes关键组件：

- Kubelet：是主节点代理。
- Kube-proxy：Service使用其将链接路由到Pod，如上文所述。
- Docker或Rocket：Kubernetes使用的容器技术来创建容器。



### Kubernetes Master

集群拥有一个Kubernetes Master（紫色方框）。Kubernetes Master提供集群的独特视角，并且拥有一系列组件，比如Kubernetes API Server。API Server提供可以用来和集群交互的REST端点。master节点包括用来创建和复制Pod的Replication Controller。





## 参考链接

[寞月 - SegmentFault 思否](https://segmentfault.com/u/lonelymoon)