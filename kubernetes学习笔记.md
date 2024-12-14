## kubernetes

### 一、基本概念

**容器化部署**：与虚拟化类似，但是共享了操作系统

> 优点：
>
> ​    可以保证每个容器拥有自己的文件系统、CPU、内存、进程空间等
>
> ​    运行应用程序所需要的资源都被容器包装，并和底层基础架构解耦
>
> ​    容器化的应用程序可以跨云服务商、跨Linux操作系统发行版进行部署

 kubernetes，是一个全新的基于容器技术的分布式架构领先方案，是谷歌严格保密十几年的秘密武器----Borg系统的一个开源版本，于2014年9月发布第一个版本，2015年7月发布第一个正式版本。

​    kubernetes的本质是**一组服务器集群**，它可以在集群的每个节点上运行特定的程序，来对节点中的容器进行管理。目的是实现资源管理的自动化，主要提供了如下的主要功能：

- **自我修复**：一旦某一个容器崩溃，能够在1秒中左右迅速启动新的容器
- **弹性伸缩**：可以根据需要，自动对集群中正在运行的容器数量进行调整
- **服务发现**：服务可以通过自动发现的形式找到它所依赖的服务
- **负载均衡**：如果一个服务起动了多个容器，能够自动实现请求的负载均衡
- **版本回退**：如果发现新发布的程序版本有问题，可以立即回退到原来的版本
- **存储编排**：可以根据容器自身的需求自动创建存储卷

#### kubernetes组件

一个kubernetes集群主要是由**控制节点(master)**、**工作节点(node)**构成，每个节点上都会安装不同的组件。

**master：集群的控制平面，负责集群的决策  (  管理  )**

> 1. ###### **etcd**：一个高可用的键值存储系统，用于保存整个集群的状态和配置信息。
> 2. ###### **API Server**：提供了 Kubernetes 资源操作的唯一入口，负责接收和处理用户的请求，并将资源的状态存储到 etcd 中，下发任务给nide的Kubelet。
> 3. ###### **Controller Manager**：包含各种控制器，如 Node Controller、Deployment Controller 等。它们负责监控资源的状态，并进行相应的调整以维持期望的状态。（自动监管资源）
> 4. ###### **Scheduler**：负责将未调度的 Pod 分配到合适的 Node 上运行。（资源计算调度管理器）

**node：集群的数据平面，负责为容器提供运行环境 ( 干活 ) **

> 1. ###### **Kubelet**：运行在每个 Node 上，负责管理该节点上的 Pod 和容器，执行如启动、停止、监控等操作，并提供3种探针进行监控检查。
>
> 2. ###### **Kube-proxy**：实现了 Kubernetes 中的服务发现和负载均衡功能。
>
> 3. ###### **Container Runtime**：如 Docker、Containerd 等，负责运行容器。

#### 插件

##### cri：例如Docker容器运行时

##### CoreDNS：给k8s内部提供内部的域名解析

##### Ingress Controller：提供7层负载均衡（k8s官方原提供4层负载均衡service）

#### 附件

##### Prometheus：普罗米修图，给集群提供完美的监控能力

##### Dashboard：仪表盘组件

##### Federation：提供跨空间的管理能力

##### ...

##### 调用流程：

首先创建一个Deployment.yaml给api server告诉kubernetes要运行的应用的副本数量、容器镜像等信息，api server接收后把信息存储到etcd中。

controller manager中的deployment controller会一直监视etcd中的deployment信息，发现有新的deployment创建时会计算需要创建的pod数量，通知scheduler去安排这些pod的运行位置。

scheduler接收任务后会查看各个node的资源使用情况，然后找一个合适的node来运行pod，并把这个决策告诉api server，后者再更新到etcd中。

被选中的node中的kubelet会从etcd中获取pod的信息，然后通过container runtime启动相应的容器（docker）。

外部想访问这个应用则通过kube-proxy，kube-proxy将请求负载均衡分发到pod上。

##### 核心概念包括以下几个主要部分：

1. **Pod**：Pod 是 Kubernetes 中最小的部署单元，它是一组一个或多个紧密相关的容器的集合。这些容器共享网络和存储资源，并且总是被一起调度和运行在同一台宿主机上。（逻辑概念，实际并不存在）
   - 一个 Pod 中的容器通常协同工作来完成一个特定的功能。
   - Pod 为容器提供了一个相对稳定的运行环境，包括网络、存储等方面。
2. **Deployment**：用于管理 Pod 的创建、更新和扩缩容。它提供了声明式的方式来定义应用的期望状态，Kubernetes 会自动处理实际状态与期望状态之间的差异。
   - 可以方便地实现应用的滚动更新，确保更新过程中服务的可用性。
3. **Service**：提供了一种抽象，使得一组 Pod 能够对外提供统一的访问入口。它通过负载均衡的方式将请求分发到后端的 Pod 上。
   - 有助于解决 Pod 的 IP 地址不固定的问题，为应用提供稳定的访问方式。
4. **Namespace**：用于对资源进行逻辑分组和隔离，不同的 Namespace 中的资源相互隔离，便于在多租户环境中进行资源管理和权限控制。
5. **ConfigMap**：用于将配置数据以键值对的形式与应用解耦，应用可以通过环境变量或挂载文件的方式获取 ConfigMap 中的配置信息。
6. **Secret**：用于存储敏感信息，如密码、密钥等，它以加密的方式存储并在使用时进行解密。
7. **Node**：指 Kubernetes 集群中的工作节点，即实际运行 Pod 的机器。
8. **Master**：是 Kubernetes 集群的控制节点，负责管理整个集群的状态和调度决策。
9. **Ingress**：用于管理外部对集群内服务的访问，实现 HTTP 和 HTTPS 路由。
10. **Label（标签）和 Selector（选择器）**：可以为资源对象添加标签来标识其特征，然后通过选择器来筛选和操作具有特定标签的资源。

##### Pause:是一个容器，是Pod内部启动的第一个容器，什么Pod都有，作用是：

1.初始化网络栈

2.挂载需要的存储卷

3.回收僵尸进程（1号进程启动了master，master启用多个worker，某天master没了，worker归属于1号进程但是不干活，就是僵尸进程）

各个Pod内部的容器与Pause容器共享名字空间（Network、PID、IPC）

每个集群有n个master和n个worker

每个worker有n个pod

每个pod有n个容器

Pod的探针类型有3种：启动探针、存活探针、就绪探针

- **startup Probe(启动探针)**：用于检查容器是否正常启动，探测时间间隔一般比较大。容器在启动状态才会使用启动探针，使用启动探针时其它探针会被禁用，直到容器启动成功为止，启动探针就会退出。如果启动探针失败，那么kubelet就会杀死容器，然后容器根据重启策略进行重启。
- **liveness Probe(存活探针)**：用于检查容器是否还在运行，探测时间间隔一般比较小。在容器正常启动后，存活探针就会一直周期性的对容器进行探测，如果存活探针失败，那么kubelet就会杀死容器，然后容器会根据重启策略进行重启。
- **readiness Probe(就绪探针)**：用于检查容器是否已经准备好接受请求，探测时间间隔一般比较小。在容器正常启动后，就绪探针也会一直周期性的对容器进行探测，如果就绪探针失败，那么就会将Service的endpoints中移除这个Pod

### 二、部署集群

##### 1.准备环境

| ip         | os          | role   | hostname     | dns              |
| ---------- | ----------- | ------ | ------------ | ---------------- |
| 10.0.0.201 | ubuntu20.04 | master | k8s-master01 | www.masert01.com |
| 10.0.0.202 | ubuntu20.04 | node   | k8s-node01   | www.node01.com   |
| 10.0.0.203 | ubuntu20.04 | node   | k8s-node02   | www.node02.com   |
| 10.0.0.204 | ubuntu20.04 | node   | k8s-node03   | www.node03.com   |

##### 2.关闭swap设备

```
#关闭当前已启用的所有Swap设备
swapoff -a

#编辑/etc/fstab配置文件，注释用于挂载Swap设备的所有行
vim /etc/fstab
.......
#/swap.img      none    swap    sw      0       0
.......

#Ubuntu20.04及以后版本需要用systemctl mask禁用systemctl --type swap列出的所有设备
systemctl --type swap
systemctl mask SWAP_DEV

reboot

#如果确实需要在节点上使用swap设备，需要设置kubeam忽略Swap启用的状态错误
vim /etc/default/kubelet
KUBELET_EXTRA_ARGS="--fail-swap-on=false"
```

##### 3.设置时钟同步

```
#如果有本地的时钟服务器，最好用本地的时间服务器
apt install chrony -y
systemctl start chrony.service
```

##### 4.关闭防火墙

kubernetes和docker在运行中会产生大量的iptables规则，为了不让系统规则跟它们混淆，直接关闭系统的规则

~~~powershell
# 1 关闭firewalld服务
[root@master ~]# systemctl stop firewalld
[root@master ~]# systemctl disable firewalld
# 2 关闭iptables服务
[root@master ~]# systemctl stop iptables
[root@master ~]# systemctl disable iptables
~~~

##### 5.禁用selinux

selinux是linux系统下的一个安全服务，如果不关闭它，在安装集群中会产生各种各样的奇葩问题

~~~powershell
# 编辑 /etc/selinux/config 文件，修改SELINUX的值为disabled
# 注意修改完毕之后需要重启linux服务
SELINUX=disabled
~~~

##### 6.修改linux的内核参数

~~~powershell
# 修改linux的内核参数，添加网桥过滤和地址转发功能
# 编辑/etc/sysctl.d/kubernetes.conf文件，添加如下配置:
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1

# 重新加载配置
sysctl -p

# 加载网桥过滤模块
modprobe br_netfilter

# 查看网桥过滤模块是否加载成功
lsmod | grep br_netfilter
~~~

##### 7.配置ipvs功能

在kubernetes中service有两种代理模型，一种是基于iptables的，一种是基于ipvs的

两者比较的话，ipvs的性能明显要高一些，但是如果要使用它，需要手动载入ipvs模块

~~~powershell
# 1 安装ipset和ipvsadm
yum install ipset ipvsadmin -y

# 2 添加需要加载的模块写入脚本文件
cat <<EOF >  /etc/sysconfig/modules/ipvs.modules
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF

# 3 为脚本文件添加执行权限
chmod +x /etc/sysconfig/modules/ipvs.modules

# 4 执行脚本文件
/bin/bash /etc/sysconfig/modules/ipvs.modules

# 5 查看对应的模块是否加载成功
lsmod | grep -e ip_vs -e nf_conntrack_ipv4
~~~

##### 8.重启服务器

上面步骤完成之后，需要重新启动linux系统

##### 9.安装docker、配置daemon.json、配置docker代理

```
vim docker_install.sh
#!/bin/bash
apt-get update && apt-get -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | apt-key add -
echo | add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
apt-get -y update
apt -y install docker-ce=5:23.0.5-1~ubuntu.22.04~jammy docker-ce-cli=5:23.0.5-1~ubuntu.22.04~jammy

#配置daemon.json优化镜像加速等
cat > /etc/docker/daemon.json << EOF
{ "registry-mirrors" : [ "https://docker.m.daocloud.io", "https://noohub.ru", "https://huecker.io", "https://dockerhub.timeweb.cloud", "https://docker.rainbond.cc" ] }
EOF

#配置docker代理
#sed -i '/\[Service\]/a \
#Environment="HTTP_PROXY=http://172.20.0.29:7980" \
#Environment="HTTPS_PROXY=https://172.20.0.29:7980"' /lib/systemd/system/docker.service

systemctl daemon-reload
systemctl restart docker.service
systemctl enable docker.service
```

##### 10.安装cri-dockerd

```
export https_proxy=172.20.0.29:7980
#下载好后用scp将包传到其它节点
curl -LO https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.1/cri-dockerd_0.3.1.3-0.ubuntu-jammy_amd64.deb
dpkg -i dpkg -i cri-dockerd_0.3.1.3-0.ubuntu-jammy_amd64.deb
systemctl enable cri-docker.service
unset https_proxy
```

##### 11.安装kubelet、kubeadm和kubectl

```
#每个节点都执行
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
apt-get update
#apt-get install -y kubelet kubeadm kubectl默认安装最新版
apt install -y kubeadm=1.27.1-00 kubectl=1.27.1-00 kubelet=1.27.1-00
systemctl enable kubelet
```

##### 12.配置cri-dockerd

```
vim /lib/systemd/system/cri-docker.service
......
ExecStart=/usr/bin/cri-dockerd --container-runtime-endpoint fd:// --network-plugin=cni --cni-bin-dir=/opt/cni/bin --cni-cache-dir=/var/lib/cni/cache --cni-conf-dir=/etc/cni/net.d --pod-infra-container-image registry.aliyuncs.com/google_containers/pause:3.6
......

systemctl daemon-reload && systemctl restart cri-docker.service
```

##### 13.配置kubelet

```
mkdir /etc/sysconfig
cat > /etc/sysconfig/kubelet << EOF
KUBELET_KUBEADM_ARGS="--container-runtime=remote --container-runtime-endpoint=/run/cri-dockerd.sock"
EOF
```

##### 14.master节点拉取相关镜像文件

```
#查看需要拉取哪些镜像文件
kubeadm config images list

#从国内仓库拉取镜像
kubeadm config images pull --cri-socket unix:///run/cri-dockerd.sock --image-repository=registry.aliyuncs.com/google_containers
```

##### 15.初始化master节点

```
kubeadm init --apiserver-advertise-address="10.0.0.160" --kubernetes-version=v1.27.1 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --token-ttl=0 --cri-socket unix:///run/cri-dockerd.sock  --upload-certs --image-repository=registry.aliyuncs.com/google_containers

docker pull  pause 重新tag成

#如果初始化失败需要重新执行初始化，执行下面的语句
kubeadm reset --cri-socket unix:///var/run/cri-dockerd.sock -f
rm -fr  $HOME/.kube/config
```



##### 16.实现kubeatl、kubeadm命令补全功能

```
vim .bashrc
.....
source <(kubectl completion bash)
source <(kubeadm completion bash)

source .bashrc
```

##### 17.Kubernetes集群管理员认证到Kubernetes集群时使用的kubeconfig配置文件

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

##### 18.安装网络插件

```
mkdir /data/kubernetes/network/flannel -pv
cd  /data/kubernetes/network/flannel
wget https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
#查看部署flannel需要的拉取的镜像
grep image: kube-flannel.yml
#部署flannel并应用
#插件有问题可以清除再重新应用kubectl delete -f kube-flannel.yml
kubectl apply -f kube-flannel.yml
#查看2个镜像是否拉取成功
docker images
```

##### 19.添加节点到集群中

```
kubeadm join 10.0.0.160:6443 --token 7pf113.ebnjsdg948zgltz8 --discovery-token-ca-cert-hash sha256:145020abb2f29ccd1f10d1614795bf80d18a0fd4d0137055950f1564e0cf970c --cri-socket=unix:///var/run/cri-dockerd.sock
kubeadm join 10.0.0.160:6443 --token xa6pw8.bv92qztv0u5qcoa6 --discovery-token-ca-cert-hash sha256:b44fad882edd5c9743ebd5ccb21d8ebc5bc0d0dd39b5e1970b954d021fd14484 --cri-socket=unix:///var/run/cri-dockerd.sock
```

##### 20.验证集群节点

```
#列出集群中所有节点的状态，ready才是正常的
kubectl get nodes
#列出集群中所有名称空间的pod信息，running才是正常的
kubectl get pods -A

#如果不是running可以用kubectl describe查看pod的信息
kubectl describe pod pod_name -n kube-system
```

### 三、网络

CNI，是一个给容器添加网络，删除网络，检查网络的一个规范，把CNI称为一个插件，借助他实现集群内部网络互相通信