---
title: k8s
createdAt: 2022-08-09
categories: 
- DevOps
tags: 
- k8s
- DevOps
---

[Kubernetes](https://kubernetes.io/) 就是一个生产级别的容器编排平台和集群管理系统，不仅能够创建、调度容器，还能够监控、管理服务器，它凝聚了 Google 等大公司和开源社区的集体智慧，从而让中小型公司也可以具备轻松运维海量计算节点——也就是“云计算”的能力。

<!--more-->

## minikube学习环境

实践出真知，这里安装[minikube](https://minikube.sigs.k8s.io)快速体验，

```shell
# Intel x86_64
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

# Apple arm64
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-arm64

sudo install minikube /usr/local/bin/

minikube version

# Install kubectl
minikube kubectl
```

在 minikube 环境里，我们会用到两个客户端：minikube 管理 Kubernetes 集群环境，kubectl 操作实际的 Kubernetes 功能，和 Docker 比起来有点复杂。

使用命令 minikube start 会从 Docker Hub 上拉取镜像，以当前最新版本的 Kubernetes 启动集群。不过为了保证实验环境的一致性，我们可以在后面再加上一个参数 --kubernetes-version，明确指定要使用 Kubernetes 版本。并且因为我们基于vm安装，且已经安装docker了，所以这里直接使用本地的docker。

```shell
minikube start --kubernetes-version=v1.23.3 --force --driver=docker --image-mirror-country='cn'
```

```shell
minikube status
minikube node list
minikube ssh
```

```shell
alias kubectl="minikube kubectl --"
# 代码补全
source <(kubectl completion bash)
kubectl version --short
```

```shell
kubectl run ngx --image=nginx:alpine
kubectl get pod
kubectl get node
kubectl get pod -n kube-system
```


## 基础架构

![[Pasted image 20220809144441.png]]

![[Pasted image 20220809211414.png]]

Kubernetes 采用了现今流行的“控制面 / 数据面”（Control Plane / Data Plane）架构，集群里的计算机被称为“节点”（Node），可以是实机也可以是虚机，少量的节点用作控制面来执行集群的管理维护工作，其他的大部分节点都被划归数据面，用来跑业务应用。

控制面的节点在 Kubernetes 里叫做 Master Node，一般简称为 Master，它是整个集群里最重要的部分，可以说是 Kubernetes 的大脑和心脏。数据面的节点叫做 Worker Node，一般就简称为 Worker 或者 Node，相当于 Kubernetes 的手和脚，在 Master 的指挥下干活。

Node 的数量非常多，构成了一个资源池，Kubernetes 就在这个池里分配资源，调度应用。因为资源被“池化”了，所以管理也就变得比较简单，可以在集群中任意添加或者删除节点。

在这张架构图里，我们还可以看到有一个 kubectl，它就是 Kubernetes 的客户端工具，用来操作 Kubernetes，但它位于集群之外，理论上不属于集群。

### Master组件

里的组件有哪些Master 里有 4 个组件，分别是 apiserver、etcd、scheduler、controller-manager。

1. apiserver 是 Master 节点——同时也是整个 Kubernetes 系统的唯一入口，它对外公开了一系列的 RESTful API，并且加上了验证、授权等功能，所有其他组件都只能和它直接通信，可以说是 Kubernetes 里的联络员。
2. etcd 是一个高可用的分布式 Key-Value 数据库，用来持久化存储系统里的各种资源对象和状态，相当于 Kubernetes 里的配置管理员。注意它只与 apiserver 有直接联系，也就是说任何其他组件想要读写 etcd 里的数据都必须经过 apiserver。
3. scheduler 负责容器的编排工作，检查节点的资源状态，把 Pod 调度到最适合的节点上运行，相当于部署人员。因为节点状态和 Pod 信息都存储在 etcd 里，所以 scheduler 必须通过 apiserver 才能获得。

4. controller-manager 负责维护容器和节点等资源的状态，实现故障检测、服务迁移、应用伸缩等功能，相当于监控运维人员。同样地，它也必须通过 apiserver 获得存储在 etcd 里的信息，才能够实现对资源的各种操作。

### Node组件

```shell
minikube ssh
docker ps | grep kube-proxy
ps -ef | grep kubelet
```

Master 里的 apiserver、scheduler 等组件需要获取节点的各种信息才能够作出管理决策，那这些信息该怎么来呢？这就需要 Node 里的 3 个组件了，分别是 kubelet、kube-proxy、container-runtime。

![[Pasted image 20220809145041.png]]

1. kubelet 是 Node 的代理，负责管理 Node 相关的绝大部分操作，Node 上只有它能够与 apiserver 通信，实现状态报告、命令下发、启停容器等功能，相当于是 Node 上的一个“小管家”。
2. kube-proxy 的作用有点特别，它是 Node 的网络代理，只负责管理容器的网络通信，简单来说就是为 Pod 转发 TCP/UDP 数据包，相当于是专职的“小邮差”。
3. 第三个组件 container-runtime 我们就比较熟悉了，它是容器和镜像的实际使用者，在 kubelet 的指挥下创建容器，管理 Pod 的生命周期，是真正干活的“苦力”。

现在，我们再把 Node 里的组件和 Master 里的组件放在一起来看，就能够明白 Kubernetes 的大致工作流程了：
1. 每个 Node 上的 kubelet 会定期向 apiserver 上报节点状态，apiserver 再存到 etcd 里。
2. 每个 Node 上的 kube-proxy 实现了 TCP/UDP 反向代理，让容器对外提供稳定的服务。
3. scheduler 通过 apiserver 得到当前的节点状态，调度 Pod，然后 apiserver 下发命令给某个 Node 的 kubelet，kubelet 调用 container-runtime 启动容器。
4. controller-manager 也通过 apiserver 得到实时的节点状态，监控可能的异常情况，再使用相应的手段去调节恢复。

### Addons插件

```shell
minikube addons list
```

DNS 你应该比较熟悉吧，它在 Kubernetes 集群里实现了域名解析服务，能够让我们以域名而不是 IP 地址的方式来互相通信，是服务发现和负载均衡的基础。由于它对微服务、服务网格等架构至关重要，所以基本上是 Kubernetes 的必备插件。Dashboard 就是仪表盘，为 Kubernetes 提供了一个图形化的操作界面，非常直观友好，虽然大多数 Kubernetes 工作都是使用命令行 kubectl，但有的时候在 Dashboard 上查看信息也是挺方便的。

```shell
minikube dashboard

kubectl proxy --port=8888 --address='192.168.3.140' --accept-hosts='^.*' &
```

### 总结

![[Pasted image 20220809151937.png]]

## Yaml

![[Pasted image 20220809152640.png]]

[YAML](https://yaml.org/) 是 JSON 的超集，支持整数、浮点数、布尔、字符串、数组和对象等数据类型。也就是说，任何合法的 JSON 文档也都是 YAML 文档，如果你了解 JSON，那么学习 YAML 会容易很多。但和 JSON 比起来，YAML 的语法更简单，形式也更清晰紧凑，比如：
1. 使用空白与缩进表示层次（有点类似 Python），可以不使用花括号和方括号。
2. 可以使用 # 书写注释，比起 JSON 是很大的改进。对象（字典）的格式与 JSON 基本相同，但 Key 不需要使用双引号。
3. 数组（列表）是使用 - 开头的清单形式（有点类似 MarkDown）。
4. 表示对象的 : 和表示数组的 - 后面都必须要有空格。
5. 可以使用 --- 在一个文件里分隔多个 YAML 对象。

```yaml
# 复杂的例子，组合数组和对象
Kubernetes:
  master:
    - apiserver: running
    - etcd: running
  node:
    - kubelet: running
    - kube-proxy: down
    - container-runtime: [docker, containerd, cri-o]
```

### API对象

因为 apiserver 是 Kubernetes 系统的唯一入口，外部用户和内部组件都必须和它通信，而它采用了 HTTP 协议的 URL 资源理念，API 风格也用 RESTful 的 GET/POST/DELETE 等等，所以，这些概念很自然地就被称为是“API 对象”了

```shell
kubectl get pod --v=9
```

从截图里可以看到，kubectl 客户端等价于调用了curl，向 8443 端口发送了 HTTP GET 请求，URL 是 /api/v1/namespaces/default/pods。

### 如何描述API对象

```shell
kubectl run ngx --image=nginx:alpine
```

我们将命令式的命令转换为声明式的yaml配置文件

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ngx-pod
  labels:
    env: demo
    owner: chrono

spec:
  containers:
  - image: nginx:alpine
    name: ngx
    ports:
    - containerPort: 80
```

```shell
kubectl apply -f ngx-pod.yml
kubectl delete -f ngx-pod.yml
```

Kubernetes 收到这份“声明式”的数据，再根据 HTTP 请求里的 POST/DELETE 等方法，就会自动操作这个资源对象，至于对象在哪个节点上、怎么创建、怎么删除完全不用我们操心。

### 如何编写

这么多 API 对象，我们怎么知道该用什么 apiVersion、什么 kind？metadata、spec 里又该写哪些字段呢

1. [官方参考文档](https://kubernetes.io/docs/reference/kubernetes-api/) YYDS
2. `kubectl api-resources`
3. kubectl explain
```shell
kubectl explain pod
kubectl explain pod.metadata
kubectl explain pod.spec
kubectl explain pod.spec.containers
```
4. 这第三个技巧就是 kubectl 的两个特殊参数 --dry-run=client 和 -o yaml，前者是空运行，后者是生成 YAML 格式，结合起来使用就会让 kubectl 不会有实际的创建动作，而只生成 YAML 文件
```shell
kubectl run ngx --image=nginx:alpine --dry-run=client -o yaml > ngx.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ngx
  name: ngx
spec:
  containers:
  - image: nginx:alpine
    name: ngx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

小技巧，这个小技巧还可以再进化一下，把这段参数定义成 Shell 变量（名字任意，比如$do/$go，这里用的是$out），用起来会更省事，比如：

```shell
export out="--dry-run=client -o yaml"
kubectl run ngx --image=nginx:alpine $out
```

## Pod

Pod 的概念也就呼之欲出了，容器正是“豆荚”里那些小小的“豌豆”，你可以在 Pod 的 YAML 里看到，“spec.containers”字段其实是一个数组，里面允许定义多个容器。

Kubernetes 让 Pod 去编排处理容器，然后把 Pod 作为应用调度部署的最小单位，Pod 也因此成为了 Kubernetes 世界里的“原子”（当然这个“原子”内部是有结构的，不是铁板一块），基于 Pod 就可以构建出更多更复杂的业务形态了。

![[Pasted image 20220809154417.png]]

![[Pasted image 20220809154427.png]]

### 用yaml描述pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busy-pod
  labels:
    owner: chrono
    env: demo
    region: north
    tier: back

spec:
  containers:
  - image: busybox:latest
    name: busy
    imagePullPolicy: IfNotPresent
    env:
      - name: os
        value: "ubuntu"
      - name: debug
        value: "on"
    command:
      - /bin/echo
    args:
      - "$(os), $(debug)"
```

1. ports：列出容器对外暴露的端口，和 Docker 的 -p 参数有点像。
2. imagePullPolicy：指定镜像的拉取策略，可以是 Always/Never/IfNotPresent，一般默认是 IfNotPresent，也就是说只有本地不存在才会远程拉取镜像，可以减少网络消耗。
3. env：定义 Pod 的环境变量，和 Dockerfile 里的 ENV 指令有点类似，但它是运行时指定的，更加灵活可配置。
4. command：定义容器启动时要执行的命令，相当于 Dockerfile 里的 ENTRYPOINT 指令。
5. args：它是 command 运行时的参数，相当于 Dockerfile 里的 CMD 指令，这两个命令和 Docker 的含义不同，要特别注意。

### kubectl操作pod

```shell
kubectl apply -f busy-pod.yml
kubectl delete -f busy-pod.yml
kubectl delete pod busy-pod

kubectl get pod
kubectl describe pod busy-pod

kubectl logs ngx-pod


echo 'aaa' > a.txt
kubectl cp a.txt ngx-pod:/tmp

kubectl exec -it ngx-pod -- sh
```

## Job/CornJob

在线业务，比如Mysql，Redis这种都是长年在线的，但是有一些内部的计算业务，一般离线运行，运行完就结束，这种叫做离线业务。

Kubernetes 为离线业务提供了 Job 和 CronJob 两种 API 对象，分别处理“临时任务”和“定时任务”。

Job 的关键字段是 spec.template，里面定义了用来运行业务的 Pod 模板，其他的重要字段有 completions、parallelism 等

其他更详细的信息可以参考 Job 文档：
1. activeDeadlineSeconds，设置 Pod 运行的超时时间。
2. backoffLimit，设置 Pod 的失败重试次数。
3. completions，Job 完成需要运行多少个 Pod，默认是 1 个。
4. parallelism，它与 completions 相关，表示允许并发运行的 Pod 数量，避免过多占用资源。
5. 要注意这 4 个字段并不在 template 字段下，而是在 spec 字段下，所以它们是属于 Job 级别的，用来控制模板里的 Pod 对象。

```shell
export out="--dry-run=client -o yaml"
kubectl create job echo-job --image=busybox $out

kubectl apply -f echo-job.yml
kubectl get job
```


![[Pasted image 20220809161624.png]]

CronJob 的关键字段是 spec.jobTemplate 和 spec.schedule，分别定义了 Job 模板和定时运行的规则。

```shell
export out="--dry-run=client -o yaml"
kubectl create cj echo-cj --image=busybox --schedule="" $out

kubectl apply -f echo-cj.yml
kubectl get cj
```

![[Pasted image 20220809161659.png]]

## ConfigMap & Secret

学习容器技术的时候讲过，可以选择两种管理配置文件的方式。第一种是编写 Dockerfile，用 COPY 指令把配置文件打包到镜像里；第二种是在运行时使用 docker cp 或者 docker run -v，把本机的文件拷贝进容器。

但这两种方式都存在缺陷。第一种方法相当于是在镜像里固定了配置文件，不好修改，不灵活，第二种方法则显得有点“笨拙”，不适合在集群中自动化运维管理。

### CofifgMap

```shell
kubectl create cm info --from-literal=k=v --dry-run=client -o yaml

kubectl create configmap redis-config --from-file=./redis.properties
```

更多信息请查阅：[官方文档](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: info

data:
  count: '10'
  debug: 'on'
  path: '/etc/systemd'
  greeting: |
    say hello to kubernetes.
```

```shell
kubectl apply -f cm.yml

kubectl get cm
kubectl describe cm info
```

你可以看到，现在 ConfigMap 的 Key-Value 信息就已经存入了 etcd 数据库，后续就可以被其他 API 对象使用。

### Secret

它和 ConfigMap 的结构和用法很类似，不过在 Kubernetes 里 Secret 对象又细分出很多类，比如：
1. 访问私有镜像仓库的认证信息
2. 身份识别的凭证信息
3. HTTPS 通信的证书和私钥
4. 一般的机密信息（格式由用户自行解释）

前几种我们现在暂时用不到，所以就只使用最后一种，创建 YAML 样板的命令是 kubectl create secret generic 

```shell
kubectl create secret generic user --from-literal=name=root --dry-run=client -o yaml
```

```yaml

apiVersion: v1
kind: Secret
metadata:
  name: user

data:
  name: cm9vdA==
  
```

既然它的名字是 Secret，我们就不能像 ConfigMap 那样直接保存明文了，需要对数据“做点手脚”。你会发现，这里的“name”值是一串“乱码”，而不是刚才在命令行里写的明文“root”。

这串“乱码”就是 Secret 与 ConfigMap 的不同之处，不让用户直接看到原始数据，起到一定的保密作用。不过它的手法非常简单，只是做了 Base64 编码，根本算不上真正的加密，所以我们完全可以绕开 kubectl，自己用 Linux 小工具“base64”来对数据编码，然后写入 YAML 文件，比如：

```shell
echo -n "123456" | base64
MTIzNDU2
```

```shell
kubectl apply  -f secret.yml
kubectl get secret
kubectl describe secret user
```

### 如何使用

因为 ConfigMap 和 Secret 只是一些存储在 etcd 里的字符串，所以如果想要在运行时产生效果，就必须要以某种方式“注入”到 Pod 里，让应用去读取。在这方面的处理上 Kubernetes 和 Docker 是一样的，也是两种途径：环境变量和加载文件。

#### 环境变量

在前面讲 Pod 的时候，说过描述容器的字段“containers”里有一个“env”，它定义了 Pod 里容器能够看到的环境变量。当时我们只使用了简单的“value”，把环境变量的值写“死”在了 YAML 里，实际上它还可以使用另一个“valueFrom”字段，从 ConfigMap 或者 Secret 对象里获取值，这样就实现了把配置信息以环境变量的形式注入进 Pod，也就是配置与应用的解耦。

```shell

kubectl explain pod.spec.containers.env.valueFrom

```

“valueFrom”字段指定了环境变量值的来源，可以是“configMapKeyRef”或者“secretKeyRef”，然后你要再进一步指定应用的 ConfigMap/Secret 的“name”和它里面的“key”，要当心的是这个“name”字段是 API 对象的名字，而不是 Key-Value 的名字。

下面我就把引用了 ConfigMap 和 Secret 对象的 Pod 列出来，给你做个示范，为了提醒你注意，我把“env”字段提到了前面：

```yaml

apiVersion: v1
kind: Pod
metadata:
  name: env-pod

spec:
  containers:
  - env:
      - name: COUNT
        valueFrom:
          configMapKeyRef:
            name: info
            key: count
      - name: GREETING
        valueFrom:
          configMapKeyRef:
            name: info
            key: greeting
      - name: USERNAME
        valueFrom:
          secretKeyRef:
            name: user
            key: name
      - name: PASSWORD
        valueFrom:
          secretKeyRef:
            name: user
            key: pwd

    image: busybox
    name: busy
    imagePullPolicy: IfNotPresent
    command: ["/bin/sleep", "300"]
```

![[Pasted image 20220809165024.png]]

#### Volume 的方式

在 Pod 里挂载 Volume 很容易，只需要在“spec”里增加一个“volumes”字段，然后再定义卷的名字和引用的 ConfigMap/Secret 就可以了。要注意的是 Volume 属于 Pod，不属于容器，所以它和字段“containers”是同级的，都属于“spec”

```yaml

spec:
  volumes:
  - name: cm-vol
    configMap:
      name: info
  - name: sec-vol
    secret:
      secretName: user
```

有了 Volume 的定义之后，就可以在容器里挂载了，这要用到“volumeMounts”字段，正如它的字面含义，可以把定义好的 Volume 挂载到容器里的某个路径下，所以需要在里面用“mountPath”“name”明确地指定挂载路径和 Volume 的名字。

```yaml
  containers:
  - volumeMounts:
    - mountPath: /tmp/cm-items
      name: cm-vol
    - mountPath: /tmp/sec-items
      name: sec-vol
```

![[Pasted image 20220809165751.png]]

完整配置，如下，

```yaml

apiVersion: v1
kind: Pod
metadata:
  name: vol-pod

spec:
  volumes:
  - name: cm-vol
    configMap:
      name: info
  - name: sec-vol
    secret:
      secretName: user

  containers:
  - volumeMounts:
    - mountPath: /tmp/cm-items
      name: cm-vol
    - mountPath: /tmp/sec-items
      name: sec-vol

    image: busybox
    name: busy
    imagePullPolicy: IfNotPresent
    command: ["/bin/sleep", "300"]
```

## 初级知识网络

![[Pasted image 20220809222024.png]]

## kubeadm搭建生产集群

[kubeadm](https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/)，原理和 minikube 类似，也是用容器和镜像来封装 Kubernetes 的各种组件，但它的目标不是单机部署，而是要能够轻松地在集群环境里部署 Kubernetes，并且让这个集群接近甚至达到生产级质量。

而在保持这个高水准的同时，kubeadm 还具有了和 minikube 一样的易用性，只要很少的几条命令，如 init、join、upgrade、reset 就能够完成 Kubernetes 集群的管理维护工作，这让它不仅适用于集群管理员，也适用于开发、测试人员。

### 实验架构

![[Pasted image 20220809224246.png]]

### 准备工作

第一，由于 Kubernetes 使用主机名来区分集群里的节点，所以每个节点的 hostname 必须不能重名。你需要修改“/etc/hostname”这个文件，把它改成容易辨识的名字，比如 Master 节点就叫 master，Worker 节点就叫 worker：

```shell
sudo vi /etc/hostname
```

第二，虽然 Kubernetes 目前支持多种容器运行时，但 Docker 还是最方便最易用的一种，所以我们仍然继续使用 Docker 作为 Kubernetes 的底层支持，使用 apt 安装 Docker Engine（可参考第 1 讲）。

安装完成后需要你再对 Docker 的配置做一点修改，在“/etc/docker/daemon.json”里把 cgroup 的驱动程序改成 systemd ，然后重启 Docker 的守护进程，具体的操作我列在了下面：

```shell
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

第三，为了让 Kubernetes 能够检查、转发网络流量，你需要修改 iptables 的配置，启用“br_netfilter”模块：

```shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward=1 # better than modify /etc/sysctl.conf
EOF

sudo sysctl --system
```

第四，你需要修改“/etc/fstab”，关闭 Linux 的 swap 分区，提升 Kubernetes 的性能：

```shell
sudo swapoff -a
sudo sed -ri '/\sswap\s/s/^#?/#/' /etc/fstab
```

### 安装kubeadm

在Master跟Worker节点都要安装

```shell

sudo apt install -y apt-transport-https ca-certificates curl

curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -

cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF

sudo apt update
```

更新了软件仓库，我们就可以用 apt install 获取 kubeadm、kubelet 和 kubectl 这三个安装必备工具了。apt 默认会下载最新版本，但我们也可以指定版本号，比如使用和 minikube 相同的“1.23.3”：

```shell

sudo apt install -y kubeadm=1.23.3-00 kubelet=1.23.3-00 kubectl=1.23.3-00

kubeadm version
kubectl version --client

```

另外按照 Kubernetes 官网的要求，我们最好再使用命令 apt-mark hold ，锁定这三个软件的版本，避免意外升级导致版本错误：

```shell

sudo apt-mark hold kubeadm kubelet kubectl

```

### 下载 Kubernetes 组件镜像

前面我说过，kubeadm 把 apiserver、etcd、scheduler 等组件都打包成了镜像，以容器的方式启动 Kubernetes，但这些镜像不是放在 Docker Hub 上，而是放在 Google 自己的镜像仓库网站 gcr.io，而它在国内的访问很困难，直接拉取镜像几乎是不可能的。

所以我们需要采取一些变通措施，提前把镜像下载到本地。使用命令 kubeadm config images list 可以查看安装 Kubernetes 所需的镜像列表，参数 --kubernetes-version 可以指定版本号：

```shell

kubeadm config images list --kubernetes-version v1.23.3

k8s.gcr.io/kube-apiserver:v1.23.3
k8s.gcr.io/kube-controller-manager:v1.23.3
k8s.gcr.io/kube-scheduler:v1.23.3
k8s.gcr.io/kube-proxy:v1.23.3
k8s.gcr.io/pause:3.6
k8s.gcr.io/etcd:3.5.1-0
k8s.gcr.io/coredns/coredns:v1.8.6

```

从国内的镜像网站下载然后再用 docker tag 改名，能够使用 Shell 编程实现自动化：

```shell

repo=registry.aliyuncs.com/google_containers

for name in `kubeadm config images list --kubernetes-version v1.23.3`; do

    src_name=${name#k8s.gcr.io/}
    src_name=${src_name#coredns/}

    docker pull $repo/$src_name

    docker tag $repo/$src_name $name
    docker rmi $repo/$src_name
done

```

### 安装Master节点

kubeadm 的用法非常简单，只需要一个命令 kubeadm init 就可以把组件在 Master 节点上运行起来，不过它还有很多参数用来调整集群的配置，你可以用 -h 查看。这里我只说一下我们实验环境用到的 3 个参数：
1. --pod-network-cidr，设置集群里 Pod 的 IP 地址段。
2. --apiserver-advertise-address，设置 apiserver 的 IP 地址，对于多网卡服务器来说很重要（比如 VirtualBox 虚拟机就用了两块网卡），可以指定 apiserver 在哪个网卡上对外提供服务。
3. --kubernetes-version，指定 Kubernetes 的版本号。

下面的这个安装命令里，我指定了 Pod 的地址段是“10.10.0.0/16”，apiserver 的服务地址是“192.168.10.210”，Kubernetes 的版本号是“1.23.3”：

```shell

sudo kubeadm init \
    --pod-network-cidr=10.10.0.0/16 \
    --apiserver-advertise-address=192.168.10.210 \
    --kubernetes-version=v1.23.3

```

```shell

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

```

意思是要在本地建立一个“.kube”目录，然后拷贝 kubectl 的配置文件，你只要原样拷贝粘贴就行。

另外还有一个很重要的“kubeadm join”提示，其他节点要加入集群必须要用指令里的 token 和 ca 证书，所以这条命令务必拷贝后保存好：

```shell

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.10.210:6443 --token tv9mkx.tw7it9vphe158e74 \
  --discovery-token-ca-cert-hash sha256:e8721b8630d5b562e23c010c70559a6d3084f629abad6a2920e87855f8fb96f3

```

安装完成后，你就可以使用 kubectl version、kubectl get node 来检查 Kubernetes 的版本和集群的节点状态了：

```shell

kubectl version
kubectl get node

```

你会注意到 Master 节点的状态是“NotReady”，这是由于还缺少网络插件，集群的内部网络还没有正常运作。

### 安装Flannel 网络插件

Kubernetes 定义了 CNI 标准，有很多网络插件，这里我选择最常用的 Flannel，可以在它的 GitHub 仓库里（https://github.com/flannel-io/flannel/）找到相关文档。

它安装也很简单，只需要使用项目的“kube-flannel.yml”在 Kubernetes 里部署一下就好了。不过因为它应用了 Kubernetes 的网段地址，你需要修改文件里的“net-conf.json”字段，把 Network 改成刚才 kubeadm 的参数 --pod-network-cidr 设置的地址段。

比如在这里，就要修改成“10.10.0.0/16”：

```shell

  net-conf.json: |
    {
      "Network": "10.10.0.0/16",
      "Backend": {
        "Type": "vxlan"
      }
    }
```

改好后，你就可以用 kubectl apply 来安装 Flannel 网络了：

```shell

kubectl apply -f kube-flannel.yml

```

稍等一小会，等镜像拉取下来并运行之后，你就可以执行 kubectl get node 来看节点状态：

```shell

kubectl get node

```

这时你应该能够看到 Master 节点的状态是“Ready”，表明节点网络也工作正常了。

### 安装Worker节点

如果你成功安装了 Master 节点，那么 Worker 节点的安装就简单多了，只需要用之前拷贝的那条 kubeadm join 命令就可以了，记得要用 sudo 来执行

```shell

sudo \
kubeadm join 192.168.10.210:6443 --token tv9mkx.tw7it9vphe158e74 \
  --discovery-token-ca-cert-hash sha256:e8721b8630d5b562e23c010c70559a6d3084f629abad6a2920e87855f8fb96f3

```

它会连接 Master 节点，然后拉取镜像，安装网络插件，最后把节点加入集群。当然，这个过程中同样也会遇到拉取镜像的问题，你可以如法炮制，提前把镜像下载到 Worker 节点本地，这样安装过程中就不会再有障碍了。Worker 节点安装完毕后，执行 kubectl get node ，就会看到两个节点都是“Ready”状态

token具有时效性，默认为24小时，如果失效了，可以使用

```shell
kubeadm token create --print-join-command
```

创建一个新的token

### 安装Console节点

后面 Console 节点的部署工作更加简单，它只需要安装一个 kubectl，然后复制“config”文件就行，你可以直接在 Master 节点上用“scp”远程拷贝，例如：

```shell
scp `which kubectl` root@192.168.10.208:~/
scp ~/.kube/config root@192.168.10.208:~/.kube
```






