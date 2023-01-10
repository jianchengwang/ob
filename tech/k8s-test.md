---
title: k8s-test
slug: k8s-test
author: [jianchengwang]
date: 2023-01-10
excerpt: "今天使用腾讯云搭建一个serverless的k8s集群，简单使用下k8s，这边做个小记"
draft: true
tags: [cloud]
---

今天使用腾讯云搭建一个serverless的k8s集群，简单使用下k8s，这边做个小记。

## kubectl config

安装kubectl，可以参考 [install-kubectl-linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)，

然后 `~/.kube/config` 黏贴腾讯云搭建好的的kubeconfig 访问凭证内容，注意腾讯云的k8s集群需要开启外网访问

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl
mv kubectl /usr/local/bin

vim ~/.kube/config  
export KUBECONFIG=~/.kube/config

kubectl config get-contexts  
kubectl config use-context cls-1v22oyfy-context-default
```

## kubectl command

### kubectl get

```bash
kubectl get node -o wide
kubectl get node -o yaml
kubectl get node -o json | jq ".items[] | {name: .metadata.name} + .status.nodeInfo"

kubectl -n work get all

kubectl cluster-info
kubectl api-resources
kubectl get pod
kubectl explain node
```

### kubeclt run

```
Usage: kubectl run NAME --image=image [--env="key=value"] [--port=port] [--replicas=replicas] [--dry-run=bool] [--overrides=inline-json] [--command] -- [COMMAND] [args...] [options]
```

**Pod** 是 K8S 中最小的调度单元，所以我们无法直接在 K8S 中运行一个 container 但是我们可以运行一个 Pod 而这个 Pod 中只包含一个 container。

`NAME` 和 `--image` 是必需项。分别代表此次部署的名字及所使用的镜像，其余部分之后进行解释。当然，在我们实际使用时，推荐编写配置文件并通过 `kubectl create` 进行部署。

```bash
kubectl run redis --image='redis:alpine'
```

### kubectl apply

```bash

kubectl apply -f namespace.yaml

kubectl -n work get all

kubectl apply -f redis


```

