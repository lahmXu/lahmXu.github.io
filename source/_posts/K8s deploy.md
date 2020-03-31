---
title: K8s deploy
tags:
  - k8s                   
categories:
- k8s 
---

## minikube部署

1. 安装机器ubuntu18,大于2核
2. 切换root用户
3. 开始教程之前先安装docker
```shell
apt install docker.io
```
4. 添加kubectl
	- step 1: 访问官方github网址：https://github.com/kubernetes/kubernetes/releases
	- step 2: 找到想使用的发布版本，在每个发布版本的最后一行有类似“CHANGELOG-1.10.md”这样的内容，点击超链进入；
	- step 3: 然后进入“Client Binaries”区域；
	- step 4: 选择和目标机器系统匹配的二进制包下载；
	- step 5: 解压缩，将kubectl放入/usr/local/bin目录；

5. 下载并配置minikube
```shell
#下载minikube国内阿里云，不要下载最新版，最新版有问题
curl -Lo minikube http://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.2.0/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/

```

6. 启动minikube
```shell
#指定不需要vm-driver
minikube start --vm-driver=none --registry-mirror=https://registry.docker-cn.com
```

7. 开启dashboard
```shell
#查看列表
$ minikube addons list

#开启dashboard,开启heapster
$ minikube dashboard
$ minikube addons enable heapster 

#查看dashboard是否安装成功
kubectl log kubernetes-dashboard-xxxxx -n kube-system

#创建svc-nodepart.yaml
cat > svc-nodepart.yaml << EOF
apiVersion: v1
kind: Service
metadata:	
  name: netty
  labels:
    app: gateway-netty
spec:
  ports:
  - port: 8843
    name: gateway-netty
    nodePort:30000
  selector:
    app: gateway-netty
  type: NodePort
EOF
#创建端口映射
kubectl apply -f svc-nodepart.yaml -n kube-system

```

参考链接：
[minikube国内安装步骤 - 简书](https://www.jianshu.com/p/18441c7434a6)
比较权威，但是仍然不对[Minikube - Kubernetes本地实验环境-云栖社区-阿里云](https://yq.aliyun.com/articles/221687?spm=a2c4e.11153940.0.0.59fb4cecfe6SsI&p=3#comments)



## microk8s(不完整)

参考链接：
[microk8s 搭建 - 肖祥 - 博客园](https://www.cnblogs.com/xiao987334176/p/10931290.html)

1. 安装snap

2. 安装最新版microk8s
  snap install microk8s —classic

3. 安装docker 
  microk8s 1.14之后就不包含docker命令了，需要的可以下载之前的版本
  snap install microk8s —classic —channel=1.13/stable

  apt-get install docker.io

### 问题解决

1. 解决heapster镜像拉取问题
必看参考链接：[microk8s安装过程中遇到的几个问题 -  - SegmentFault 思否](https://segmentfault.com/a/1190000019534913?utm_source=tag-newest)
```shell
#拉取国内镜像
docker pull mirrorgooglecontainers/$imageName:$imageVersion
#重新标记
docker tag  mirrorgooglecontainers/$imageName:$imageVersion k8s.gcr.io/$imageName:$imageVersion
#保存到本地
docker save k8s.gcr.io/$imageName:$imageVersion > $imageName.tar

microk8s.ctr -n k8s.io image import $imageName.tar



#拉取国内镜像重新打包
docker pull mirrorgooglecontainers/pause:3.1
docker pull mirrorgooglecontainers/heapster-influxdb-amd64:v1.3.3
docker pull mirrorgooglecontainers/heapster-grafana-amd64:v4.4.3
docker pull mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.8.3
docker pull mirrorgooglecontainers/heapster-amd64:v1.5.2
docker pull mirrorgooglecontainers/k8s-dns-dnsmasq-nanny-amd64:1.14.7
docker pull mirrorgooglecontainers/k8s-dns-kube-dns-amd64:1.14.7
docker pull mirrorgooglecontainers/k8s-dns-sidecar-amd64:1.14.7

docker tag mirrorgooglecontainers/pause:3.1 k8s.gcr.io/pause:3.1
docker tag mirrorgooglecontainers/heapster-influxdb-amd64:v1.3.3 k8s.gcr.io/heapster-influxdb-amd64:v1.3.3
docker tag mirrorgooglecontainers/heapster-grafana-amd64:v4.4.3 k8s.gcr.io/heapster-grafana-amd64:v4.4.3
docker tag mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.8.3 k8s.gcr.io/kubernetes-dashboard-amd64:v1.8.3
docker tag mirrorgooglecontainers/heapster-amd64:v1.5.2 k8s.gcr.io/heapster-amd64:v1.5.2
docker tag mirrorgooglecontainers/k8s-dns-dnsmasq-nanny-amd64:1.14.7 gcr.io/google_containers/k8s-dns-dnsmasq-nanny-amd64:1.14.7
docker tag mirrorgooglecontainers/k8s-dns-kube-dns-amd64:1.14.7 gcr.io/google_containers/k8s-dns-kube-dns-amd64:1.14.7
docker tag mirrorgooglecontainers/k8s-dns-sidecar-amd64:1.14.7 gcr.io/google_containers/k8s-dns-sidecar-amd64:1.14.7

docker save k8s.gcr.io/pause > pause.tar
docker save k8s.gcr.io/heapster-influxdb-amd64 > heapster-influxdb-amd64.tar
docker save k8s.gcr.io/heapster-grafana-amd64 > heapster-grafana-amd64.tar
docker save k8s.gcr.io/kubernetes-dashboard-amd64 > kubernetes-dashboard-amd64.tar
docker save k8s.gcr.io/heapster-amd64 > heapster-amd64.tar
docker save gcr.io/google_containers/k8s-dns-dnsmasq-nanny-amd64 > k8s-dns-dnsmasq-nanny-amd64.tar
docker save gcr.io/google_containers/k8s-dns-kube-dns-amd64 > k8s-dns-kube-dns-amd64.tar
docker save gcr.io/google_containers/k8s-dns-sidecar-amd64 > k8s-dns-sidecar-amd64.tar

microk8s.ctr -n k8s.io image import pause.tar
microk8s.ctr -n k8s.io image import heapster-influxdb-amd64.tar
microk8s.ctr -n k8s.io image import heapster-grafana-amd64.tar
microk8s.ctr -n k8s.io image import kubernetes-dashboard-amd64.tar
microk8s.ctr -n k8s.io image import heapster-amd64.tar
microk8s.ctr -n k8s.io image import k8s-dns-dnsmasq-nanny-amd64.tar
microk8s.ctr -n k8s.io image import k8s-dns-kube-dns-amd64.tar
microk8s.ctr -n k8s.io image import k8s-dns-sidecar-amd64.tar
```











## kbadmin部署

1. 准备工作
2. 所有节点安装工具（1,2两步所有节点都需要操作）;
3. 部署Kuberneter Master;
4. 部署Kubernetes Worker;
5. 部署Dashboard可视化插件;
6. 部署容器存储插件;





### 1.准备工作

1. 关闭防火墙

	```shell
	$ sudo ufw disable
	```


2. 禁用SELINUX

  	```shell
   $ sudo vi /etc/selinux/config
   SELINUX=permissive   
   ```

3. 开启数据包转发

   1. 内核开启ipv4转发

      ```shell
		$ sudo vim /etc/sysctl.conf
      net.ipv4.ip_forward = 1  #开启ipv4转发，允许内置路由
      #使之生效
      $ sudo sysctl -p
      ```

   2. 防火墙修改FORWARD链默认策略

      设置docker启动参数添加`--iptables=false`选项，使docker不再操作iptables，比如1.10版以上可编辑docker daemon默认配置文件`/etc/docker/daemon.json`:

      ```shell
      {
          "iptables": false
      }
      ```
  
4. 禁用swap

   ```shell
   $ sudo swapoff -a 
   #同时还需要修改/etc/fstab文件，注释掉 SWAP 的自动挂载，防止机子重启后swap启用。
   ```

   

5. 配置iptables参数，使得流经网桥的流量也经过iptables/netfilter防火墙

   ```shell
   $ sudo tee /etc/sysctl.d/k8s.conf <<-'EOF'
   net.bridge.bridge-nf-call-ip6tables = 1
   net.bridge.bridge-nf-call-iptables = 1
   EOF
   
   $ sudo sysctl --system
   ```

   

### 2.所有节点安装工具

1. docker

```shell
#### 卸载旧docker
$ sudo apt-get remove docker docker-engine docker.io 

#### 安装依赖，使得apt可以使用https
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
    
#添加docker的GPG key
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

#设置docker镜像源
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

#安装指定版本docker-ce
$ sudo apt-get install -y docker-ce=5:18.09.0~3-0~ubuntu-bionic

#设置开机启动docker
$ sudo systemctl enable docker && sudo systemctl start docker

#将当前登录用户加入到docker用户组中
$ sudo usermod -aG docker xxx

#docker启动参数配置
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
 "iptables": false,
 "ip-masq": false,
 "storage-driver": "overlay2",
 "graph": "/home/docker"
}
EOF

$ sudo systemctl restart docker

```

2. 下载Kubernetes镜像

```shell
docker pull richarddockerimage/kube-proxy-v15
docker pull richarddockerimage/kube-scheduler-v15
docker pull richarddockerimage/kube-controller-manager-v15
docker pull richarddockerimage/kube-apiserver-v15
docker pull richarddockerimage/etcd
docker pull richarddockerimage/pause-v15
docker pull richarddockerimage/coredns-v15

docker tag richarddockerimage/kube-proxy-v15 k8s.gcr.io/kube-proxy:v1.15.0
docker tag richarddockerimage/kube-scheduler-v15 k8s.gcr.io/kube-scheduler:v1.15.0
docker tag richarddockerimage/kube-controller-manager-v15 k8s.gcr.io/kube-controller-manager:v1.15.0
docker tag richarddockerimage/kube-apiserver-v15 k8s.gcr.io/kube-apiserver:v1.15.0
docker tag richarddockerimage/etcd k8s.gcr.io/etcd:3.3.10
docker tag richarddockerimage/pause-v15 k8s.gcr.io/pause:3.1
docker tag richarddockerimage/coredns-v15 k8s.gcr.io/coredns:1.3.1

docker rmi richarddockerimage/kube-proxy-v15
docker rmi richarddockerimage/kube-scheduler-v15
docker rmi richarddockerimage/kube-controller-manager-v15
docker rmi richarddockerimage/kube-apiserver-v15
docker rmi richarddockerimage/etcd
docker rmi richarddockerimage/pause-v15
docker rmi richarddockerimage/coredns-v15
```



### 3.部署Kuberneter Master

1. 安装kubeadm、kubelet、kubectl

```shell
#创建kubernetes的source文件
$ sudo apt-get update && sudo apt-get install -y apt-transport-https curl

$ sudo curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -

$ sudo tee /etc/apt/sources.list.d/kubernetes.list <<-'EOF'
deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main
EOF

$ sudo apt-get update

#安装指定版本的kudeadm kubelet kubectl
$ apt install -y kubelet=1.15.0-00 kubeadm=1.15.0-00 kubectl=1.15.0-00

#设置开机启动
$ sudo systemctl enable kubelet && sudo systemctl start kubelet
```



2. 初始化kubernetes

```shell
$ kubeadm init --kubernetes-version=v1.15.0 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12

#配置集群管理员
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config

#记录加入集群的令牌
kubeadm join 10.3.0.50:6443 --token w1bc9h.wjhqbki4xc7drvoe \
    --discovery-token-ca-cert-hash sha256:b2ebcbee78bdf675e3a2ea8b5676f5e1ab3f5496eefe3732c6abca507ea7af84
    
#部署网络插件
$ kubectl apply -f https://git.io/weave-kube-1.6
```



### 4.部署Kubernetes Worker

1. 安装kubelet、kubeadm

```shell
$ cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

$ apt-get install -y kubelet=1.15.0-00 kubeadm=1.15.0-00
$ systemctl enable kubelet
```

2. 加入集群

```shell
#直接执行master记录的集群令牌
$ kubeadm join 10.3.0.50:6443 --token w1bc9h.wjhqbki4xc7drvoe \
    --discovery-token-ca-cert-hash sha256:b2ebcbee78bdf675e3a2ea8b5676f5e1ab3f5496eefe3732c6abca507ea7af84
```

问题：子节点看不到所有node运行状态，master上可以看到？？