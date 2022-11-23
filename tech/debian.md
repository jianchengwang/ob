---
title: debian
slug: debian
author: [jianchengwang]
date: 2019-10-09
excerpt: "新公司配置的笔记本是 `Win` 系统，因为本人习惯 `Linux` 系统，所以就装了个 `Debian` 子系统玩一下。"
draft: false
tags: 
- linux
---

新公司配置的笔记本是 `Win` 系统，因为本人习惯 `Linux` 系统，所以就装了个 `Debian` 子系统玩一下。

最早之前用 `Deepin` 是基于 `Debian` 的，后面用 `Manjaro` 了，忘得差不多了，这里做个记录，方便以后查阅。

### wsl2

```shell
# 开启window子系统
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
# 启用虚拟机
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
# 下载liux核心更新套件
wget https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
# 查看子系统的wsl版本
wsl -l -v
# 更改子系统的wsl版本
wsl --set-version <distribution name> <versionNumber>
# 将wsl2设置为默认版本
wsl --set-default-version 2

# 子系统迁移到非系统盘 https://github.com/DDoSolitary/LxRunOffline
LxRunOffline.exe move -n Debian -d D:\wsl2\Debian

# 安装多个相同子系统
LxRunOffline.exe d -n Debian -d D:\wsl2\Debian-1\ -N Debian-1

# 重启子系统
net stop LxssManager
net start LxssManager

# 进入子系统
bash

# 访问宿主机目录
cd /mnt

# 宿主机访问wsl2
\\wsl$\Debian\
```

这个如果是外部访问的话，使用golang等编辑器编辑wsl2内部的文件，容易出现在WSL中如果有和Windows中相同名称的命令,可能会出现调用紊乱，

```shell
echo $PATH
```

会看到大量window的path变量，所以这里配置下，wsl.conf

```shell
sudo vim /etc/wsl.conf
# no load windows path
[interop]
appendWindowsPath = false
#[automount]
#enabled = false
wsl --terminate Debian
```

### set sources.list

```shell
mv /etc/apt/sources.list /etc/apt/sources.list.bak
sudo vim /etc/apt/sources.list

deb http://mirrors.cloud.tencent.com/debian/ buster main non-free contrib
deb http://mirrors.cloud.tencent.com/debian-security buster/updates main
deb http://mirrors.cloud.tencent.com/debian/ buster-updates main non-free contrib
deb http://mirrors.cloud.tencent.com/debian/ buster-backports main non-free contrib

deb-src http://mirrors.cloud.tencent.com/debian-security buster/updates main
deb-src http://mirrors.cloud.tencent.com/debian/ buster main non-free contrib
deb-src http://mirrors.cloud.tencent.com/debian/ buster-updates main non-free contrib
deb-src http://mirrors.cloud.tencent.com/debian/ buster-backports main non-free contrib

sudo apt-get update
```

### apt-get & dpkg

```shell
apt-get update # update sources
apt-get upgrade # update all installed packages
apt-get dist-upgrade # update system

apt-get install packagename # install

apt-get remove packagename # keep config
apt-get -purge remove packagename # delete config
apt-get autoclean apt # clean deleted backup packages
apt-get clean # clean all backup packages
apt-get autoclean # clean deleted .deb

apt-cache show package

```

### install soft

```shell
# ssh
sudo apt-get install openssh-server
sudo vim /etc/ssh/sshd_config # 设置端口为2222，避免与宿主机端口冲突
service ssh start
/etc/init.d/ssh start 
/etc/init.d/ssh status
update-rc.d ssh enable # 开机自启
update-rc.d ssh disabled

# dev tools
sudo apt-get install net-tools
sudo apt-get install curl
sudo apt-get install wget
sudo apt-get install git
# 如果该项保持启用状态，由于行尾结束符的差异，此设置可能导致你未编辑的文件中出现了修改操作
git config --global core.autocrlf false 
git config --global user.name "jianchengwang"
git config --global user.email "jiancheng_wang@yahoo.com"

# proxychains4，防火墙要允许ssr应用通过
sudo apt-get install -y proxychains4
ip route | grep default | awk '{print $3}'
sudo vim /etc/proxychains4.conf
proxychains4 curl www.google.com

# zsh
sudo apt-get install zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
chsh -s /bin/zsh

# or fish
sudo apt-add-repository ppa:fish-shell/release-3
sudo apt-get update
sudo apt-get install fish
chsh -s /bin/fish
# install oh-my-fish
curl -L https://get.oh-my.fish | fish
# or install by git clone
git clone https://github.com/oh-my-fish/oh-my-fish
cd oh-my-fish
bin/install --offline
# omf command 
omf install clearance
omf theme sushi

# neovim
sudo apt-get install neovim
vim .vimrc
set mouse=v # xshell vim鼠标右键无法复制问题

# vscode wsl remote
code-insiders 

# java
wget jdk-8u221-linux-x64.tar.gz
tar -zxvf jdk-8u221-linux-x64.tar.gz
sudo vim /etc/profile
export JAVA_HOME=/home/wjc/lang/jdk1.8.0_221
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
source /etc/profile

# mysql
wget https://dev.mysql.com/get/mysql-apt-config_0.8.13-1_all.deb
sudo dpkg -i mysql-apt-config_0.8.13-1_all.deb 
sudo apt-get update
sudo apt-get install mysql-server
sudo service mysql start
mysql -u root -p
CREATE USER 'wjc'@'%' IDENTIFIED BY '123456';
GRANT ALL PRIVILEGES ON *.* TO 'wjc'@'%';
FLUSH PRIVILEGES;
sudo vim /etc/mysql/mysql.conf.d/mysqlld.cnf
bind-address    = 0.0.0.0

# docker
sudo apt-get update
sudo apt-get upgrade -y  
sudo apt install docker.io
sudo service docker start
sudo usermod -aG docker user-name # 添加用户到docker组
# 配置镜像加速
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://ed47r0z0.mirror.aliyuncs.com"]
}
EOF
# https://github.com/WhitewaterFoundry/Pengwin/issues/485
# The docker installer uses iptables for nat. Unfortunately Debian uses a modified version of nftables. You can convert the entries over to nftables or just setup Debian to use the legacy iptables.
sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
# 重启docker
sudo dockerd
sudo service docker restart
 
```

