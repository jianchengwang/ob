---
title: arm-conf
slug: arm-conf
author: [jianchengwang]
date: 2023-01-11
excerpt: "公司跟华为云有合作，机器都是arm架构的，这边对arm架构的机器配置做一个简单的记录。"
draft: true
tags: [cloud]
---

## yum install

yum install 出现 `Loaded plugins: fastestmirror`

```bash
vim /etc/yum/pluginconf.d/fastestmirror.conf
enabled=0

vim /etc/yum.conf
plugins=0

wget http://mirrors.aliyun.com/repo/Centos-altarch-7.repo -O etc/yum.repos.d/CentOS-Base.repo
yum clean all 
rm -rf /var/cache/yum 
yum makecache
```

## docker-install

```bash
#!/bin/bash  
  
# Get real path  
BASEDIR=$(cd `dirname $0` && pwd)  
cd ${BASEDIR}  
  
# Log Location on Server.  
LOG_LOCATION=${BASEDIR}  
exec > >(tee -i $LOG_LOCATION/install.`date +%Y%m%d%H%M%S`.log)  
exec 2>&1  
  
  
# 安装Docker  
yum remove docker \  
                  docker-client \  
                  docker-client-latest \  
                  docker-common \  
                  docker-latest \  
                  docker-latest-logrotate \  
                  docker-logrotate \  
                  docker-engine  
# 安装依赖包  
yum install -y yum-utils \  
  device-mapper-persistent-data \  
  lvm2  
# 配置Docker安装包仓库  
yum-config-manager \  
    --add-repo \  
    https://download.docker.com/linux/centos/docker-ce.repo  
# 安装社区版  
yum -y install docker-ce docker-ce-cli containerd.io  
# 加入开机自启  
systemctl enable docker  
# 启动Docker服务  
systemctl start docker  
# 测试Docker  
docker version  
docker run hello-world  
  
# 添加镜像加速  
mkdir -p /etc/docker  
tee /etc/docker/daemon.json <<-'EOF'  
{  
  "registry-mirrors": ["https://ed47r0z0.mirror.aliyuncs.com", "https://registry.docker-cn.com"]
}  
EOF 

# systemctl daemon-reload  
systemctl restart docker  
  
# 安装docker compose  
curl -L "https://github.com/docker/compose/releases/download/v2.15.1/docker-compose-linux-aarch64" -o /usr/local/bin/docker-compose  
chmod +x /usr/local/bin/docker-compose  
docker-compose version
```

## var/lib/docker

```bash
systemctl stop docker.socket
systemctl stop docker.service
mkdir -p /data/docker/lib
yum install rsync -y
rsync -avzP /var/lib/docker /data/docker/lib/
ln -fs /data/docker/lib /var/lib/docker
or
vim /etc/docker/daemon.json
"data-root": "/data/docker/lib",
```

## server

```shell
docker pull arm64v8/mysql
docker pull arm64v8/redis
docker pull arm64v8/nginx

vim redis.conf
ignore-warnings ARM64-COW-BUG
```





