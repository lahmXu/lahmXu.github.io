---
title: K8s Service
tags:
  - k8s                   
categories:
- k8s 
---



问题：
访问k8s集群中的pod， 客户端需要知道pod地址，需要感知pod的状态。那如何获取各个pod的地址？若某一node上的pod故障，客户端如何感知？



## Service功能
- 发现后端pod服务
- 为一组具有相同功能的容器应用提供一个统一的入口地址
- 将请求进行负载分发到后端的各个容器应用上的控制器



##### Kubernetes Service 是一个抽象层，它定义了一组逻辑的Pods，借助Service，应用可以方便的实现服务发现与负载均衡。


![](/images/6844FAEA-F1A1-412F-BB10-6D85EF08D6EB.png)





## Service类型

- **ClusterIP**：提供一个集群内部的虚拟IP以供Pod访问（service默认类型)
**Headless Service**
不需要分配VIP，直接以**DNS记录的方式解析出被代理的Pod的IP地址**
```shell
<pod-name>.<svc-name>.<namespace>.svc.cluster.local
```
所以k8s内部的访问地址可以直接配置成这样的域名进行访问。
- **NodePort**:在每个Node上打开一个端口以供外部访问
- **LoadBalancer**：通过外部的负载均衡器来访问
- ***ExternalName**：通过返回 CNAME 和它的值，可以将服务映射到 externalName 字段的内容，没有任何类型代理被创建。


## Service selector
- service通过selector和pod建立关联。
- k8s会根据service关联到pod的podIP信息组合成一个endpoint。
- 若service定义中没有selector字段，service被创建时，endpoint controller不会自动创建endpoint。


## Service负载分发策略
- RoundRobin：轮询模式，即轮询将请求转发到后端的各个pod上（默认模式）
- SessionAffinity：基于客户端IP地址进行会话保持的模式，第一次客户端访问后端某个pod，之后的请求都转发到这个pod上。



## 参考链接:
[浅谈 kubernetes service 那些事 - 知乎](https://zhuanlan.zhihu.com/p/39909011)