---
title: k8s-saythx
slug: k8s-saythx
author: [jianchengwang]
date: 2023-01-10
excerpt: "今天使用kubeadm的k8s集群，简单使用下k8s部署[saythx](https://github.com/tao12345666333/saythx)这个测试项目，这边做个小记。"
draft: true
tags: [cloud]
---

今天使用kubeadmin搭建k8s集群，简单使用下k8s部署[saythx](https://github.com/tao12345666333/saythx)这个测试项目，这边做个小记。

## kubeadm

```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF

apt-get update
apt-get install -y docker.io kubeadm
```

## kubectl

安装kubectl，可以参考 [install-kubectl-linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)，

然后 `~/.kube/config` 黏贴腾讯云搭建好的的kubeconfig访问凭证内容，注意腾讯云的k8s集群需要开启外网访问

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl
mv kubectl /usr/local/bin

vim ~/.kube/config  
export KUBECONFIG=~/.kube/config

kubectl config get-contexts  
kubectl config use-context cls-1v22oyfy-context-default
```


```bash
kubectl get node -o wide
kubectl get node -o yaml
kubectl get node -o json | jq ".items[] | {name: .metadata.name} + .status.nodeInfo"

kubectl cluster-info
kubectl api-resources
kubectl get pod
kubectl explain node

kubectl apply -f namespace.yaml

kubectl -n work get all

kubectl apply -f redis-deployment.yaml

kubectl -n work exec -it saythx-redis-6df6d6c566-2mcb5 bash
```

拉取测试项目

```bash
git clone https://github.com/tao12345666333/saythx.git
```


## 构造镜像

### fe

我们使用了前端框架 [Vue](https://vuejs.org/)，所以在做生产部署时，需要先在 [Node JS](https://nodejs.org/) 的环境下进行打包构建。包管理器使用的是 [Yarn](https://yarnpkg.com/)。然后使用 [Nginx](https://nginx.com/) 提供服务，并进行反向代理，将请求正确的代理至后端。

```Dockerfile
FROM node:10.13 as builder

WORKDIR /app
COPY . /app

RUN yarn install \
        && yarn build


FROM nginx:1.15

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/dist /usr/share/nginx/html/

EXPOSE 80
```

Nginx 的配置文件如下：

```nginx
upstream backend-up {
    server saythx-backend:8080;
}
server {
    listen       80;
    server_name  localhost;

    charset     utf-8;

    location / {
        root /usr/share/nginx/html;
        try_files $uri $uri/ /index.html;
    }

    location ~ ^/(api) {
        proxy_pass   http://backend-up;
    }
}
```

将 API 的请求反向代理到后端服务上。其余请求全部留给前端进行处理。

### be

后端是使用 [Golang](https://golang.org/) 编写的 API 服务，对请求进行相应处理，并将数据存储至 [Redis](http://redis.io/) 当中。依赖管理使用的是 [dep](https://github.com/golang/dep)。由于 Golang 是编译型语言，编译完成后会生成一个二进制文件，为了让镜像尽可能小，所以 Dockerfile 和前端的差不多，都使用了[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)的特性。

```Dockerfile
FROM golang:1.11.1 as builder

WORKDIR /go/src/be
COPY . /go/src/be
RUN go get -u github.com/golang/dep/cmd/dep \
        && dep ensure \
        && go build

FROM debian:stretch-slim
COPY --from=builder /go/src/be/be /usr/bin/be
ENTRYPOINT ["/usr/bin/be"]
EXPOSE 8080
```

注意这里会暴露出来后端服务所监听的端口。

### work

Work 端使用的是 [Python](https://python.org/)，用于计算已经存储至 Redis 当中的数据，并生成排行榜。依赖使用 [pip](https://github.com/pypa/pip) 进行安装。

```Dockerfile
FROM python:3.7-slim

WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt

ENTRYPOINT ["python", "work.py"]
```

## 构建发布

接下来，我们只要在对应项目目录中，执行 `docker build [OPTIONS] PATH` 即可。一般我们会使用 `-t name:tag` 的方式打 tag。

```bash
# 以下分别是在各模块自己的目录内 
be docker build -t taobeier/saythx-be . 
fe docker build -t taobeier/saythx-fe . 
work docker build -t taobeier/saythx-work .

docker push taobeier/saythx-be 
docker push taobeier/saythx-fe 
docker push taobeier/saythx-work
```

## Docker Compose

```yaml
version: '3'

services:
  saythx-frontend:
    build:
      context: fe/.
    image: taobeier/saythx-fe
    ports:
      - "8088:80"
    depends_on:
      - saythx-backend
    networks:
      - saythx

  saythx-backend:
    build:
      context: be/.
    image: taobeier/saythx-be
    depends_on:
      - saythx-redis
    networks:
      - saythx
    environment:
      - REDIS_HOST=saythx-redis
    
  saythx-work:
    build:
      context: work/.
    image: taobeier/saythx-work
    depends_on:
      - saythx-redis
    networks:
      - saythx
    environment:
      - REDIS_HOST=saythx-redis
      - REDIS_PORT=6379

  saythx-redis:
    image: "redis:5"
    networks:
      - saythx

networks:
  saythx:
```


## k8s配置文件

如果已经有docker-compose.yml，可以使用 [kompose](https://github.com/kubernetes/kompose) 转换为k8s的配置文件，这里采用直接编写k8s配置文件部署

### namespace.yaml

```yaml
apiVersion: v1 
kind: Namespace 
metadata: 
	name: work
```

指定了 `Namespace` name 为 `work`。然后进行部署

```bash
kubectl apply -f namespace.yaml 
```

### redis-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis
  name: saythx-redis
  namespace: work
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - image: redis:5
        name: redis
        ports:
        - containerPort: 6379

```

- `apiVersion` ：指定了 API 的版本号，当前我们使用的 K8S 中， `Deployment` 的版本号为 `apps/v1`，而在 1.9 之前使用的版本则为 `apps/v1beta2`，在 1.8 之前的版本使用的版本为 `extensions/v1beta1`。在编写配置文件时需要格外注意。
- `kind` ：指定了资源的类型。这里指定为 `Deployment` 说明是一次部署。
- `metadata` ：指定了资源的元信息。例如其中的 `name` 和 `namespace` 分别表示资源名称和所归属的 `Namespace`。
- `spec` ：指定了对资源的配置信息。例如其中的 `replicas` 指定了副本数当前指定为 1 。`template.spec` 则指定了 `Pod` 中容器的配置信息，这里的 `Pod` 中只部署了一个容器。

配置文件已经生产，现在对它进行部署。

```bash
kubectl -n work get all 
kubectl apply -f redis-deployment.yaml
kubectl -n work get all
```

可以看到 `Pod` 已经在正常运行了。我们进入 `Pod` 内进行测试。

```bash
kubectl -n work exec -it saythx-redis-79d8f9864d-x8fp9 bash
```

### redis-service.yaml

由于 `Redis` 是后端服务的依赖，我们将它作为 `Service` 暴露出来。

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: redis
  name: saythx-redis
  namespace: work
spec:
  ports:
  - protocol: TCP
    port: 6379
    targetPort: 6379
  selector:
    app: redis
  type: NodePort
```

```bash
kubectl apply -f redis-service.yaml
kubectl get svc -n work
```

### backend-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: backend
  name: saythx-backend
  namespace: work
spec:
  selector:
    matchLabels:
      app: backend
  replicas: 1
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - env:
        - name: REDIS_HOST
          value: saythx-redis
        image: taobeier/saythx-be
        name: backend
        ports:
        - containerPort: 8080
```

```bash
kubectl apply -f backend-deployment.yaml
```

### backend-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: backend
  name: saythx-backend
  namespace: work
spec:
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
  selector:
    app: backend
  type: NodePort
```

```bash
kubectl apply -f backend-service.yaml
```

### frontend-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: frontend
  name: saythx-frontend
  namespace: work
spec:
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - image: taobeier/saythx-fe
        name: frontend
        ports:
        - containerPort: 80
```

```bash
kubectl apply -f frontend-deployment.yaml
```

### frontend-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: frontend
  name: saythx-frontend
  namespace: work
spec:
  ports:
  - name: "80"
    port: 80
    targetPort: 80
  selector:
    app: frontend
  type: NodePort
```

### work-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: work
  name: saythx-work
  namespace: work
spec:
  selector:
    matchLabels:
      app: work
  replicas: 1
  template:
    metadata:
      labels:
        app: work
    spec:
      containers:
      - env:
        - name: REDIS_HOST
          value: saythx-redis
        - name: REDIS_PORT
          value: "6379"
        image: taobeier/saythx-work
        name: work
```

```bash
kubectl apply -f work-deployment.yaml
kubectl -n work scale --replicas=2 deployment/saythx-work
```

## helm

[Helm](https://www.helm.sh/) 是构建于 K8S 之上的包管理器，可与我们平时接触到的 `Yum`，`APT`，`Homebrew` 或者 `Pip` 等包管理器相类比。

使用 Helm 可简化包分发，安装，版本管理等操作流程。同时它也是 CNCF 孵化项目。

```bash
wget https://get.helm.sh/helm-v3.11.0-rc.1-linux-amd64.tar.gz
tar -zxvf helm-v3.11.0-rc.1-linux-amd64.tar.gz 
sudo chmod +x linux-amd64/helm
sudo mv linux-amd64/helm /usr/local/bin/helm
```

### Chart

```bash
helm create saythx

tree
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

#### Chart.yaml

```
apiVersion: v2
name: saythx
description: A Helm chart for Kubernetes

# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application

# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
# Versions are expected to follow Semantic Versioning (https://semver.org/)
version: 0.1.0

# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application. Versions are not expected to
# follow Semantic Versioning. They should reflect the version the application is using.
# It is recommended to use it with quotes.
appVersion: "1.16.0"
```

- `apiVersion`：目前版本为 `v2`
- `appVersion`：这是应用的版本号，需要与 `apiVersion`， `version` 等字段注意区分
- `name`: 通常要求 `chart` 的名字必须和它所在目录保持一致，且此字段必须
- `version`：表明当前 `chart` 的版本号，会直接影响 `Release` 的记录，且此字段必须
- `description`：描述

#### charts

`charts` 文件夹是用于存放依赖的 `chart` 的。当有依赖需要管理时，可添加 `requirements.yaml` 文件，可用于管理项目内或者外部的依赖。

#### .helmignore

`.helmignore` 类似于 `.gitignore` 和 `.dockerignore` 之类的，用于忽略掉一些不想包含在 `chart` 内的文件。

#### templates

`templates` 文件夹内存放着 `chart` 所使用的模板文件，也是 `chart` 的实际执行内容。在使用 `chart` 进行安装的时候，会将 下面介绍的 `values.yaml` 中的配置项与 `templates` 中的模板进行组装，生成最终要执行的配置文件。

`templates` 中，推荐命名应该清晰，如 `xx-deployment.yaml`，中间使用 `-` 进行分割，避免使用驼峰式命名。

`Notes.txt` 文件在 `helm install` 完成后，会进行回显，可用于解释说明如何访问服务等。

#### values.yaml

`values.yaml` 存放着项目的一些可配置项，如镜像的名称或者 tag 之类的。作用就是用于和模板进行组装。

### helm install

Helm 的 `chart` 可以直接在源码目录下通过 `helm install` 完成部署

```bash
helm install saythx saythx/
```

### helm package

当然，我们也可以将 `chart` 打包，以便于分发。

```bash
helm package saythx
```

可以看到打包时是按照 `chart` 的名字加版本号进行命名的。

至于部署，和前面没什么太大区别， `helm install saythx saythx-0.1.0.tgz` 即可。

### access service

```bash
kubectl get nodes --namespace work -o jsonpath="{.items[0].status.addresses[0].address}"

kubectl get --namespace work -o jsonpath="{.spec.ports[0].nodePort}" services saythx-frontend
```





