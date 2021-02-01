---
title: K8s Controller
tags:
  - k8s                   
categories:
- k8s 
---



**编排其实很简单之谈谈“控制器”模型**



### Pod这个看似复杂的API对象，实际上就是对容器的进一步抽象和封装而已



Kubernetes项目中的一个通用编排模式，即：控制循环（control loop），伪代码如下

```go
for{
  实际状态 := 获取集群中对象 X 的实际状态（Actual State）
  期望状态 := 获取集群中对象 X 的期望状态（Desired State）
  if 实际状态 == 期望状态 {
    什么都不做
  } else {
    执行编排动作，将实际状态调整为期望状态
  }
}
```

实际状态往往来自于Kubernetes集群本身。

期望状态一般来自于用户提交的YAML文件。



以Deployment为例，简单描述一下它对控制器模型的实现：

1. Deployment控制器从Etcd中获取到所有携带了“app:nginx”标签的Pod，然后统计它们的数量，这个就是实际状态；

2. Deployment对象的Replicas字段的值就是期望状态；

3. Deployment控制器将两个状态做比较，然后根据比较结果，确定是创建Pod，还是删除已有的Pod。

**这个操作，通常被叫做调谐（Reconcile）。这个调谐的过程，则被称作“Reconcile Loop(调谐循环)“或者”Sync Loop“(同步循环)**




## 控制器类型
1. Deployment&ReplicaSet
	- 无状态应用
2. StatefulSet
	- 有状态应用
3. DaemonSet
	- 这个 Pod 运行在 Kubernetes 集群里的每一个节点（node）上
	- 每个节点上只有一个这样的Pod实例
	- 当有新的node加入k8s集群后，该pod会被自动创建；当旧节点删除后，上面的pod会被回收；