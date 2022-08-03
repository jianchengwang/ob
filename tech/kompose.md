---
title: kompose
createdAt: 2022-04-10
categories: 
- k8s
tags:
- k8s

---

之前项目部署都是使用`docker-compsoe`，后面客户那边使用`k8s`集群，所以就需要进行转换，这边参照官方文档[translate-compose-kubernetes](https://kubernetes.io/zh/docs/tasks/configure-pod-container/translate-compose-kubernetes/)

<!--more-->

## minikube
这里使用[minikube](https://minikube.sigs.k8s.io/docs/start/)搭建简易的本地k8s集群环境进行测试。

```shell
# 安装
curl -Lo minikube https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.23.1/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
# 启动，默认使用docker容器
minikube start
# 出现报错 The "docker" driver should not be used with root privileges
minikube start --force --driver=docker
# alias
alias kubectl="minikube kubectl --"
```

## kompose

[kompose](https://github.com/kubernetes/kompose)

```shell
curl -L https://github.com/kubernetes/kompose/releases/download/v1.24.0/kompose-linux-amd64 -o /usr/local/bin/kompose
chmod +x /usr/local/bin/kompose 

cd kompose-demo
ls -al
drwxr-xr-x   5 root root 4096 Apr 10 09:12 app
-rw-r--r--   1 root root 1076 Apr 10 09:25 docker-compose-admin-demo.yml

kompose convert -f docker-compose-admin-demo.yml --volumes hostPath
ls -al
drwxr-xr-x   5 root root 4096 Apr 10 09:12 app
-rw-r--r--   1 root root 1076 Apr 10 09:25 docker-compose-admin-demo.yml
-rw-r--r--   1 root root 2453 Apr 10 10:08 kongfu-admin-demo-deployment.yaml
-rw-r--r--   1 root root  437 Apr 10 10:08 kongfu-admin-demo-service.yaml

```

`kongfu-admin-demo-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    kompose.cmd: kompose convert -f docker-compose-admin-demo.yml --volumes hostPath
    kompose.version: 1.24.0 (4a2a0458)
  creationTimestamp: null
  labels:
    io.kompose.service: kongfu-admin-demo
  name: kongfu-admin-demo
spec:
  ports:
    - name: "7092"
      port: 7092
      targetPort: 7092
  selector:
    io.kompose.service: kongfu-admin-demo
status:
  loadBalancer: {}             
```

## satrt application

 启动服务

```shell
kubectl apply -f kongfu-admin-demo-service.yaml
```

如果你在开发过程中使用 `minikube`，请执行：

```shell
minikube service kongfu-admin-demo
```

否则，我们要查看一下你的服务使用了什么 IP！

```shell
kubectl describe svc kongfu-admin-demo
```

```none
Name:              kongfu-admin-demo
Namespace:         default
Labels:            io.kompose.service=kongfu-admin-demo
Annotations:       kompose.cmd: kompose convert -f docker-compose-admin-demo.yml --volumes hostPath
                   kompose.version: 1.24.0 (4a2a0458)
Selector:          io.kompose.service=kongfu-admin-demo
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.104.57.36
IPs:               10.104.57.36
Port:              7092  7092/TCP
TargetPort:        7092/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>
```

如果你使用的是云提供商，你的 IP 将在 `LoadBalancer Ingress` 字段给出。

```shell
curl http://10.104.57.36
```

## kubectl

```shell
# 集群信息
kubectl cluster-info
# 获取pod信息，pod调度的最小单位，docker(或其他类型容器) + pause
kubectl get pods
# 运行deployment，
kubectl run d1 --image httpd:alpine --port 6091
# deployment控制pod的数量，编辑
kubectl edit deployment d1
# 查看deployment
kubectl get pod
kubectl get deployment
# 多个deployment可以expose为service
kubectl expose deployment d1 --target-port 80 --type NodePort
# 查看服务信息
kubectl get svc
```

