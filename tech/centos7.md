---
title: centos7
slug: centos7
author: [jianchengwang]
date: 2019-11-28
excerpt: "因为公司服务器都是centos7 服务器系统，所以这里对centos 7 的常规配置做一个简要的记录，方便后期维护。这里我用vmware 简单安装一个centos7 简单的演示。"
draft: false
tags: 
- linux
---

因为公司服务器都是centos7 服务器系统，所以这里对centos 7 的常规配置做一个简要的记录，方便后期维护。这里我用vmware 简单安装一个centos7 简单的演示。

## 基础知识

### 安装基础软件

```shell
# 安装gcc
yum -y install gcc

# 使用ifconfig等命令
yum -y install net-tools

# 下载文件相关
yum -y install wget

# 测试网页交互
yum -y install curl

# 编辑配置文件
yum -y install vim

# ssh and sftp
yum -y install openssh-server

vim /etc/ssh/sshd_config # 配置sshd

service sshd start or systemctl start sshd  # 启动sshd服务

ps -e | grep sshd # 检查sshd服务是否启动

netstat -an | grep 22 # 检查22端口是否监听

systemctl enable sshd # 开启自启动sshd

# 安装git
sudo yum install git
# 如果该项保持启用状态，由于行尾结束符的差异，此设置可能导致你未编辑的文件中出现了修改操作
git config --global core.autocrlf false 
git config --global user.name "jianchengwang"
git config --global user.email "jiancheng_wang@yahoo.com"

# 安装zsh
sudo yum install zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
chsh -s /bin/zsh

# 安装proxychain4
git clone https://github.com/rofl0r/proxychains-ng
cd proxychains-ng
./configure --prefix=/usr --sysconfdir=/etc
make 
make install
make install-config
cd .. && rm -rf proxychains-ng
vim /etc/proxychains.conf 
socks5  192.168.20.19 7890   
http    192.168.20.19 7890
```



### 配置镜像源

```shell

# 列出默认镜像源
[root@localhost ~]# ll /etc/yum.repos.d
total 32
-rw-r--r--. 1 root root 1664 Apr 29 00:35 CentOS-Base.repo
-rw-r--r--. 1 root root 1309 Apr 29 00:35 CentOS-CR.repo
-rw-r--r--. 1 root root  649 Apr 29 00:35 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  314 Apr 29 00:35 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Apr 29 00:35 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 Apr 29 00:35 CentOS-Sources.repo
-rw-r--r--. 1 root root 4768 Apr 29 00:35 CentOS-Vault.repo

# 备份
[root@localhost yum.repos.d]#  mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup


# 下载 163 or aliyun yum 镜像源配置文件
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo 
or 
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

# 如果出现 wget: unable to resolve host address ‘mirrors.aliyun.com’
nmcli connection show
NAME    UUID                                  TYPE      DEVICE 
ens33   9086459f-a00c-4612-8123-3cc893e4f4a9  ethernet  ens33  
virbr0  c5782ea3-b8df-4fc6-87ae-ed6a6439028a  bridge    virbr0 

nmcli con mod ens33 ipv4.dns "114.114.114.114 8.8.8.8"
nmcli con up ens33

# yum makecache
yum makecache

# yum update
yum -y update
```



### 常用命令

#### 系统命令

```shell
# 查看系统内核
cat /proc/version or uname -a
 
# su
su - # 切换到root权限（与su有区别）

# shutdown
shutdown -h now	# 关机
shutdown -r now	# 重启

# top
top	# 罗列使用CPU资源最多的linux任务 （输入q退出）

# pstree
pstree	# 以树状图显示程序

# man
man ping	# 查看参考手册（例如ping 命令）

# cal
cal -3	# 显示前一个月，当前月以及下一个月的月历
cal 10 1988	# 显示指定月，年的月历

```

#### 磁盘管理

```shell

# df
df -h	# 显示磁盘的使用情况

# du
du -h file # 查看文件容量
du -h --max-depth=1 # 查看当前目录大小

# fdisk
fdisk -l # 显示所有分区信息

# mkfs
mkfs -t ext3 /dev/hdc6 # 对分区格式化系统

# fsck
fsck -C -f -t ext3 /dev/hdc6 # 强制检测/dev/hdc6分区

# mount
mount /dev/hdc6 /mnt/hdc6 # 将分区/dev/hdc6挂载到/mnt/hdc6目录
umount /dev/hdc6 # 卸载



```

#### 文件目录

```shell
# cd
cd /home	# 进入 /home 目录
cd ..		# 返回上一级目录
cd ../.. 	# 返回上两级目录
cd -		# 返回上次所在目录
cp file1 file2	# 将file1复制为file2
cp -a dir1 dir2	# 复制一个目录
cp -a /tmp/dir1 .	# 复制一个目录到当前工作目录（.代表当前目录）

# ls
ls		# 查看目录中的文件
ls -a	# 显示隐藏文件
ls -l	# 显示详细信息
ls -lrt	# 按时间显示文件（l表示详细列表，r表示反向排序，t表示按时间排序）

# pwd
pwd		# 显示工作路径

# mkdir
mkdir dir1	# 创建 dir1 目录
mkdir dir1 dir2	# 同时创建两个目录
mkdir -p /tmp/dir1/dir2	# 创建一个目录树
mv dir1 dir2	# 移动/重命名一个目录
rm -f file1	# 删除 ‘file1’
rm -rf dir1	# 删除 ‘dir1’ 目录及其子目录内容
```

#### 文本操作

```shell
# cat
cat file1	# 从第一个字节开始正向查看文件的内容

# tac
tac file1	# 从最后一行开始反向查看一个文件的内容

# head
head -2 file1	# 查看一个文件的前两行

# tail
tail -3 file1	# 查看一个文件的最后三行

# more
more file1	# 查看一个长文件的内容

```

#### 文本处理

```shell
# grep
grep str /tmp/test	# 在文件 /tmp/test 中查找 str
grep ^str /tmp/test	# 在文件 /tmp/test 中查找以 str 开始的行
grep [0-9] /tmp/test	# 查找 /tmp/test 文件中所有包含数字的行
grep str -r /tmp/*	# 在目录 /tmp 及其子目录中查找 str

# diff
diff file1 file2	# 找出两个文件的不同处

# sdiff
sdiff file1 file2	# 以对比的方式显示两个文件的不同
```

#### 文件查询

```shell

# find
find / -name file1	# 从 / 开始进入根文件系统查找文件和目录
find / -user user1	# 查找属于用户 user1 的文件和目录
find /home/user1 -name *.bin	# 在目录 / home/user1 中查找以 .bin 结尾的文件
find /usr/bin -type f -atime +100	# 查找在过去100天内未被使用过的执行文件
find /usr/bin -type f -mtime -10	# 查找在10天内被创建或者修改过的文件

find -name '*.[ch]' | xargs grep -E 'expr'	# 在当前目录及其子目录所有.c和.h文件中查找 expr
find -type f -print0 | xargs -r0 grep -F 'expr'	# 在当前目录及其子目录的常规文件中查找 expr
find -maxdepth 1 -type f | xargs grep -F 'expr'	# 在当前目录中查找 expr

# locate
locate *.ps	寻找以 .ps 结尾的文件，先运行 updatedb 命令
```

#### 文件权限

```shell

# chown
chown -R user1 /usr/meng # 将/usr/meng下的所有目录文件归属为user1

# chmod
chmod 777 -R dir/ # 修改dir目录下所有文件权限未777
chmod u+x,g+w f01　　# 为文件f01设置自己可以执行，组员可以写入的权限
chmod u=rwx,g=rw,o=r f01
chmod 764 f01
chmod a+x f01　　# 对文件f01的u,g,o都设置可执行属性
```

#### 压缩解压

```shell
# bzip2
bzip2 file1	# 压缩 file1
bunzip2 file1.bz2	# 解压 file1.bz2

# gzip
gzip file1	# 压缩 file1
gzip -9 file1	# 最大程度压缩 file1
gunzip file1.gz	# 解压 file1.gz

# tar
tar -cvf archive.tar file1	# 把file1打包成 archive.tar（-c: 建立压缩档案；-v: 显示所有过程；-f: 使用档案名字，是必须的，是最后一个参数）
tar -cvf archive.tar file1 dir1	# 把 file1，dir1 打包成 archive.tar
tar -tf archive.tar	# 显示一个包中的内容
tar -xvf archive.tar	# 释放一个包
tar -xvf archive.tar -C /tmp	# 把压缩包释放到 /tmp目录下
tar -xzf archive.tar.gz # 解压并释放
tar -xjf archive.tar.bz2 # 解压并释放

# zip
zip file1.zip file1	# 创建一个zip格式的压缩包
zip -r file1.zip file1 dir1	# 把文件和目录压缩成一个zip格式的压缩包
unzip file1.zip	# 解压一个zip格式的压缩包到当前目录
unzip test.zip -d /tmp/	# 解压一个zip格式的压缩包到 /tmp 目录
```

#### 软件安装

```shell
# rpm
rpm –i file.rpm	# 安装软件包
rpm –q	rpm # 查询软件包
rpm -e rpm # 移除软件包

# yum
yum --help
yum repolist all # 列出所有仓库
yum info [packeage] # 显示软件包信息
yum -y install [package]	# 下载并安装一个rpm包
yum localinstall [package.rpm]	# 安装一个rpm包，使用你自己的软件仓库解决所有依赖关系
yum -y update	# 更新当前系统中安装的所有rpm包
yum update [package]	# 更新一个rpm包
yum remove [package]	# 删除一个rpm包
yum list	# 列出当前系统中安装的所有包
yum search [package]	# 在rpm仓库中搜寻软件包
yum clean [package]	# 清除缓存目录（/var/cache/yum）下的软件包
yum clean headers	# 删除所有头文件
yum clean all	# 删除所有缓存的包和头文件
```

#### 网络管理

```shell
# ifconfig
ifconfig eth0	# 显示一个以太网卡的配置
ifconfig eth0 192.168.1.1 netmask 255.255.255.0	#配置网卡的IP地址

# ifup
ifdown eth0	# 禁用 eth0 网络设备
ifup eth0	# 启用 eth0 网络设备

# iwconfig
iwconfig eth1	# 显示一个无线网卡的配置
iwlist scan		# 显示无线网络

# ip
ip addr show	# 显示网卡的IP地址

# traceroute 
traceroute # 路由追踪

# netstat
netstat -ap | grep ssh # 找出进程运行的端口

netstat -an | grep ':80' # 找出运行在指定端口的进程

netstat -i # 显示网络接口列表


# wget
wget downloadurl # 下载文件

# curl
curl www.baidu.com # 显示百度网页信息
```

#### 进程管理

```shell
# ps
ps -e | grep sshd # 检查sshd服务是否启动

# pidof
pidof nginx	# 查看nginx的进程号

# kill
kill -9 pid # 强制杀死进程

# pkill
pkill java # 杀死java进程
```

#### 系统服务

```shell


# 使某服务自动启动

chkconfig --level 3 httpd on

systemctl enable   httpd.service


# 使某服务不自动启动

chkconfig --level 3 httpd off

systemctl disable httpd.service


# 检查服务状态

service httpd status

systemctl status httpd.service （服务详细信息） systemctl is-active   httpd.service （仅显示是否   Active)

# 显示所有已启动的服务

chkconfig --list

systemctl list-units --type=service  

# 启动某服务

service httpd start

systemctl start httpd.service


# 停止某服务

service httpd stop

systemctl stop httpd.service


# 重启某服务

service httpd restart

systemctl restart httpd.service

# 防火墙
systemctl start firewalld

systemctl stop firewalld

```

#### 防火墙

firewall-cmd 是 firewalld的字符界面管理工具，firewalld是centos7的一大特性，最大的好处有两个：支持动态更新，不用重启服务；第二个就是加入了防火墙的“zone”概念。

```shell
firewall-cmd --version  # 查看版本
firewall-cmd --help     # 查看帮助

# 查看设置：
firewall-cmd --state  # 显示状态
firewall-cmd --get-active-zones  # 查看区域信息
firewall-cmd --get-zone-of-interface=eth0  # 查看指定接口所属区域
firewall-cmd --panic-on  # 拒绝所有包
firewall-cmd --panic-off  # 取消拒绝状态
firewall-cmd --query-panic  # 查看是否拒绝

firewall-cmd --reload # 更新防火墙规则
firewall-cmd --complete-reload
# 两者的区别就是第一个无需断开连接，就是firewalld特性之一动态添加规则，第二个需要断开连接，类似重启服务


# 将接口添加到区域，默认接口都在public
firewall-cmd --zone=public --add-interface=eth0
# 永久生效再加上 --permanent 然后reload防火墙

# 设置默认接口区域，立即生效无需重启
firewall-cmd --set-default-zone=public

# 查看所有打开的端口：
firewall-cmd --zone=dmz --list-ports

# 加入一个端口到区域：
firewall-cmd --zone=dmz --add-port=8080/tcp
# 加入多个端口
firewall-cmd --zone=dmz --add-port=8080-8081/tcp

# 若要永久生效方法同上

# 打开一个服务，类似于将端口可视化，服务需要在配置文件中添加，/etc/firewalld 目录下有services文件夹，这个不详细说了，详情参考文档
firewall-cmd --zone=work --add-service=smtp

# 移除服务
firewall-cmd --zone=work --remove-service=smtp

# 显示支持的区域列表
firewall-cmd --get-zones

# 设置为家庭区域
firewall-cmd --set-default-zone=home

# 查看当前区域
firewall-cmd --get-active-zones

# 设置当前区域的接口
firewall-cmd --get-zone-of-interface=enp03s

# 显示所有公共区域（public）
firewall-cmd --zone=public --list-all

# 临时修改网络接口（enp0s3）为内部区域（internal）
firewall-cmd --zone=internal --change-interface=enp03s

# 永久修改网络接口enp03s为内部区域（internal）
firewall-cmd --permanent --zone=internal --change-interface=enp03s
```

服务管理

```shell
# 显示服务列表  
Amanda, FTP, Samba和TFTP等最重要的服务已经被FirewallD提供相应的服务，可以使用如下命令查看：

firewall-cmd --get-services

# 允许SSH服务通过
firewall-cmd --new-service=ssh

# 禁止SSH服务通过
firewall-cmd --delete-service=ssh

# 打开TCP的8080端口
firewall-cmd --enable ports=8080/tcp
firewall-cmd --enable ports=3306/tcp


# 临时允许Samba服务通过600秒
firewall-cmd --enable service=samba --timeout=600

# 显示当前服务
firewall-cmd --list-services

# 添加HTTP服务到内部区域（internal）
firewall-cmd --permanent --zone=internal --add-service=http
firewall-cmd --reload     # 在不改变状态的条件下重新加载防火墙
```

端口管理

```shell
# 打开443/TCP端口
firewall-cmd --add-port=443/tcp

# 永久打开3306/TCP端口
firewall-cmd --permanent --add-port=3306/tcp

# 永久打开端口好像需要reload一下，临时打开好像不用，如果用了reload临时打开的端口就失效了
# 其它服务也可能是这样的，这个没有测试
firewall-cmd --reload

# 查看防火墙，添加的端口也可以看到
firewall-cmd --list-all
```

直接模式

```shell
# FirewallD包括一种直接模式，使用它可以完成一些工作，例如打开TCP协议的9999端口

firewall-cmd --direct -add-rule ipv4 filter INPUT 0 -p tcp --dport 9000 -j ACCEPT
firewall-cmd --reload
```

**控制端口 / 服务**

可以通过两种方式控制端口的开放，一种是指定端口号另一种是指定服务名。虽然开放 http 服务就是开放了 80 端口，但是还是不能通过端口号来关闭，也就是说通过指定服务名开放的就要通过指定服务名关闭；通过指定端口号开放的就要通过指定端口号关闭。还有一个要注意的就是指定端口的时候一定要指定是什么协议，tcp 还是 udp。知道这个之后以后就不用每次先关防火墙了，可以让防火墙真正的生效。

```shell
firewall-cmd --add-service=mysql        # 开放mysql端口
firewall-cmd --remove-service=http      # 阻止http端口
firewall-cmd --list-services            # 查看开放的服务
firewall-cmd --add-port=3306/tcp        # 开放通过tcp访问3306
firewall-cmd --remove-port=80tcp        # 阻止通过tcp访问3306
firewall-cmd --add-port=233/udp         # 开放通过udp访问233
firewall-cmd --list-ports               # 查看开放的端口
```

伪装 IP

```shell
firewall-cmd --query-masquerade # 检查是否允许伪装IP
firewall-cmd --add-masquerade   # 允许防火墙伪装IP
firewall-cmd --remove-masquerade# 禁止防火墙伪装IP
```

**端口转发**

端口转发可以将指定地址访问指定的端口时，将流量转发至指定地址的指定端口。转发的目的如果不指定 ip 的话就默认为本机，如果指定了 ip 却没指定端口，则默认使用来源端口。 如果配置好端口转发之后不能用，可以检查下面两个问题：

1. 比如我将 80 端口转发至 8080 端口，首先检查本地的 80 端口和目标的 8080 端口是否开放监听了
2. 其次检查是否允许伪装 IP，没允许的话要开启伪装 IP

```shell
firewall-cmd --add-forward-port=port=80:proto=tcp:toport=8080   # 将80端口的流量转发至8080
firewall-cmd --add-forward-port=port=80:proto=tcp:toaddr=192.168.0.1 # 将80端口的流量转发至192.168.0.1
firewall-cmd --add-forward-port=port=80:proto=tcp:toaddr=192.168.0.1:toport=8080 # 将80端口的流量转发至192.168.0.1的8080端口
```

1. 当我们想把某个端口隐藏起来的时候，就可以在防火墙上阻止那个端口访问，然后再开一个不规则的端口，之后配置防火墙的端口转发，将流量转发过去。
2. 端口转发还可以做流量分发，一个防火墙拖着好多台运行着不同服务的机器，然后用防火墙将不同端口的流量转发至不同机器。

### 安装应用软件

#### jdk1.8

```shell
wget https://code.aliyun.com/kar/oracle-jdk/raw/3c932f02aa11e79dc39e4a68f5b0483ec1d32abe/jdk-8u251-linux-x64.tar.gz
tar -zxvf jdk-8u251-linux-x64.tar.gz
vim /etc/profile
export JAVA_HOME=/root/lang/jdk1.8.0_251
export JRE_HOME=${JAVA_HOME}/jre   
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=$PATH:${JAVA_HOME}/bin
source /etc/profile
```

#### mysql5.7

```shell
# 添加Mysql5.7仓库
sudo rpm -ivh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
or
wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
yum localinstall mysql57-community-release-el7-11.noarch.rpm

# 确认Mysql仓库成功添加
sudo yum repolist all | grep mysql | grep enabled
如果展示像下面,则表示成功添加仓库:
mysql-connectors-community/x86_64  MySQL Connectors Community    enabled:     51
mysql-tools-community/x86_64       MySQL Tools Community         enabled:     63
mysql57-community/x86_64           MySQL 5.7 Community Server    enabled:    267

# 开始安装Mysql5.7
sudo yum -y install mysql-community-server

# 启动Mysql
sudo systemctl start mysqld

# 设置系统启动时自动启动
sudo systemctl enable mysqld

# 查看启动状态
sudo systemctl status mysqld

# Mysql的安全设置
CentOS上的root默认密码可以在文件/var/log/mysqld.log找到，通过下面命令可以打印出来
cat /var/log/mysqld.log | grep -i 'temporary password'
执行下面命令进行安全设置，这个命令会进行设置root密码设置，移除匿名用户，禁止root用户远程连接等
mysql_secure_installation

# 设置数据库编码为utf8
打开配置文件
sudo vim /etc/my.cnf
在[mysqld]，[client]，[mysql]节点下添加编码设置

[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
max_allowed_packet = 20M

# 重启Mysql即可
sudo systemctl restart mysqld

# 免密登录
# mysql5.7
编辑 /etc/my.cnf，在 [mysqld] 小节下添加一行：skip-grant-tables=1
# mysql5.7.6以后版本默认不安装mysqld_safe
systemctl stop mysqld
systemctl set-environment MYSQLD_OPTS="--skip-grant-tables"
systemctl start mysqld
# 修改密码
SET PASSWORD = PASSWORD('Wjc123456');
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
update user set authentication_string = password('123456'), password_expired = 'N', password_last_changed = now() where user = 'root';

# 去除密码校验
SET GLOBAL validate_password_policy=LOW;
SET GLOBAL validate_password_length = 6;
SET GLOBAL validate_password_number_count = 0;
或者
[mysqld]
validate_password_policy=LOW
validate_password_special_char_count=0
validate_password_length=0
validate_password_mixed_case_count=0
validate_password_number_count=0
或者
uninstall plugin validate_password;

# 添加远程用户
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
flush privileges;
use mysql;
select host, user from user;
```

#### redis

```shell
# add the EPEL repository, and update YUM to confirm your change:
sudo yum install epel-release
sudo yum update

# 安装
sudo yum install redis

# 启动
sudo systemctl start redis.service

# 开机启动
sudo systemctl enable redis.service

# 修改密码
vim /etc/redis.conf
# bind 127.0.0.1 // 注释掉允许远程访问
requirepass 123456

# 启动cli
redis-cli
AUTH 123456 // 验证密码
INFO replication // 配置信息
```



#### rabbitMQ

```shell
# 安装erlang
sudo yum install erlang

# 安装rabbitMQ
wget  http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.15/rabbitmq-server-3.6.15-1.el7.noarch.rpm
sudo yum install socat // 安装依赖
sudo rpm -ivh rabbitmq-serve+r-3.6.17-1.el7.noarch.rpm

# 开机启动
chkconfig rabbitmq-server on
# 启动
service rabbitmq-server start

# 安装web管理界面
sudo rabbitmq-plugins enable rabbitmq_management
# 创建用户
sudo rabbitmqctl add_user admin 123456
# 设置管理员角色
sudo rabbitmqctl set_user_tags admin administrator
# 授权
sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
# 访问
http://127.0.0.1:15672/
```

#### nfs

```shell
# 服务端配置 192.168.1.200
# 检查nfs是否安装
rpm -qa | grep nfs
yum install nfs-utils -y
# 检查rpcbind是否安装
rpm -qa | grep rpcbind
yum install rpcbind  -y
# 服务端配置
vi /etc/exports
# 这个配置表示开放本地存储目录/tmp/data只允许192.168.1.100这个主机有访问权限
# rw表示允许读写
# no_root_squash表示root用户具有完全的管理权限
# no_all_squash表示保留共享文件的UID和GID，此项是默认不写也可以；
# async表示数据可以先暂时在内存中，不是直接写入磁盘，可以提高性能，另外也可以配置sync表示数据直接同步到磁盘
/home/data 192.168.1.100(rw,no_root_squash,no_all_squash,async)
# 启动服务配置生效
service rpcbind start
service nfs start
exportfs -r
# 配置防火墙规则
# 111和2049端口，这是portmap和nfsd进程对应的端口。但是其他端口是系统开机时随机分配的，很难确定，所有需要修改配置文件/etc/sysconfig/nfs,把这些端口固定下来，然后在防火墙上关闭。
rpcinfo  -p
rpcinfo nfs_server_ip
# 设置固定端口
vim /etc/sysconfig/nfs
RQUOTAD_PORT=30001
LOCKD_TCPPORT=30002
LOCKD_UDPPORT=30002
MOUNTD_PORT=30003
STATD_PORT=30004
# 重启服务
sysctl -p
systemctl restart rpcbind 
systemctl restart nfs-server 
systemctl restart nfs-lock 
systemctl restart nfs-idmap
# 开启防火墙端口
# 开启端口访问
firewall-cmd --permanent --zone=public --add-port 111/tcp
firewall-cmd --permanent --zone=public --add-port 111/udp
firewall-cmd --permanent --zone=public --add-port 2049/tcp
firewall-cmd --permanent --zone=public --add-port 2049/udp
firewall-cmd --permanent --zone=public --add-port=30001-30004/tcp
firewall-cmd --permanent --zone=public --add-port=30001-30004/udp
firewall-cmd --reload
# 客户端配置 192.168.1.100
# 安装nfs-utils
yum -y install nfs-utils
# 关闭防火墙或者配置防火墙规则
service iptables stop
# 创建挂载点
mkdir /mnt/data1
# 挂载目录
mount -t nfs 122.51.87.176:/tmp/data /mnt/data1 -o proto=tcp -o nolock
mount -t nfs 192.168.1.200:/tmp/data /mnt/data1 -o proto=tcp -o nolock
# 查看服务器共享目录信息
showmount -e 192.168.1.200
showmount -e 122.51.87.176
# 查看挂载情况
df -h
# 卸载目录
umount /mnt/data1
```

## VM搭建测试环境

本地测试集群环境，一般使用虚拟机，这边使用vmware虚拟机，

### 镜像

这边使用[CentOS-7-x86_64-Minimal-1908](http://ftp.tku.edu.tw/Linux/CentOS/7.7.1908/isos/x86_64/)，镜像比较小，镜像安装后系统后配置下网络，

网络这边使用**桥接模式**，就是跟主机的ip网段一致，如果出现主机ping不通的情况，比如我这边是无线连接，查看网卡适配器属性，

**DNE LightWeight Filter** 不要打勾即可，可参照[VMWare桥接模式 与物理机互ping不通 终极解决方案](https://link.zhihu.com/?target=http%3A//www.franksoho.com/index.php/thread/5.html)

### 配置网络

```shell
# 配置静态ip
vi /etc/sysconfig/network-scripts/ifcfg-ens
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.3.130
GATEWAY=192.168.3.1
DNS1=114.114.114.114

# 重启网络
service network restart
# 执行初始化脚本
curl -O http://pigx.vip/os7init.sh
sh os7init.sh wjc

# 清空登录日志
echo "" > /var/log/secure
```

### docker

```shell
# 安装docker
curl -O -k https://tmp.jianchengwang.info/docker-install.sh
sh docker-install.sh

# 查看docker占用空间
docker system df
du -hs /var/lib/docker/
docker image prune
docker system prune
```

### 克隆

根据自己需要，克隆多个虚拟机即可，然后建议拍个快照，后面可以测试完可以直接恢复

### 一键脚本

这里使用pigx作者的脚本，os7init.sh，可以根据自己需要自行修改配置，

```shell
#!/bin/bash
# init centos7  ./centos7-init.sh 主机名

# 检查是否为root用户，脚本必须在root权限下运行
if [[ "$(whoami)" != "root" ]]; then
    echo "please run this script as root !" >&2
    exit 1
fi
echo -e "\033[31m the script only Support CentOS_7 x86_64 \033[0m"
echo -e "\033[31m system initialization script, Please Seriously. press ctrl+C to cancel \033[0m"

# 检查是否为64位系统，这个脚本只支持64位脚本
platform=`uname -i`
if [ $platform != "x86_64" ];then
    echo "this script is only for 64bit Operating System !"
    exit 1
fi

if [ "$1" == "" ];then
    echo "The host name is empty."
    exit 1
else
	hostnamectl  --static set-hostname  $1
	hostnamectl  set-hostname  $1
fi

cat << EOF
+---------------------------------------+
|   your system is CentOS 7 x86_64      |
|           start optimizing            |
+---------------------------------------+
EOF
sleep 1

# 安装必要支持工具及软件工具
yum_update(){
yum update -y
yum install -y nmap unzip wget vim lsof xz net-tools iptables-services ntpdate ntp-doc psmisc
}

# 设置时间同步 set time
zone_time(){
timedatectl set-timezone Asia/Shanghai
/usr/sbin/ntpdate 0.cn.pool.ntp.org > /dev/null 2>&1
/usr/sbin/hwclock --systohc
/usr/sbin/hwclock -w
cat > /var/spool/cron/root << EOF
10 0 * * * /usr/sbin/ntpdate 0.cn.pool.ntp.org > /dev/null 2>&1
* * * * */1 /usr/sbin/hwclock -w > /dev/null 2>&1
EOF
chmod 600 /var/spool/cron/root
/sbin/service crond restart
sleep 1
}

# 修改文件打开数 set the file limit
limits_config(){
cat > /etc/rc.d/rc.local << EOF
#!/bin/bash

touch /var/lock/subsys/local
ulimit -SHn 1024000
EOF

sed -i "/^ulimit -SHn.*/d" /etc/rc.d/rc.local
echo "ulimit -SHn 1024000" >> /etc/rc.d/rc.local

sed -i "/^ulimit -s.*/d" /etc/profile
sed -i "/^ulimit -c.*/d" /etc/profile
sed -i "/^ulimit -SHn.*/d" /etc/profile

cat >> /etc/profile << EOF
ulimit -c unlimited
ulimit -s unlimited
ulimit -SHn 1024000
EOF

source /etc/profile
ulimit -a
cat /etc/profile | grep ulimit

if [ ! -f "/etc/security/limits.conf.bak" ]; then
    cp /etc/security/limits.conf /etc/security/limits.conf.bak
fi

cat > /etc/security/limits.conf << EOF
* soft nofile 1024000
* hard nofile 1024000
* soft nproc  1024000
* hard nproc  1024000
hive   - nofile 1024000
hive   - nproc  1024000
EOF

if [ ! -f "/etc/security/limits.d/20-nproc.conf.bak" ]; then
    cp /etc/security/limits.d/20-nproc.conf /etc/security/limits.d/20-nproc.conf.bak
fi

cat > /etc/security/limits.d/20-nproc.conf << EOF
*          soft    nproc     409600
root       soft    nproc     unlimited
EOF

sleep 1
}

# 优化内核参数 tune kernel parametres
sysctl_config(){
if [ ! -f "/etc/sysctl.conf.bak" ]; then
    cp /etc/sysctl.conf /etc/sysctl.conf.bak
fi

#add
cat > /etc/sysctl.conf << EOF
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv4.tcp_syn_retries = 1
net.ipv4.tcp_synack_retries = 1
net.ipv4.tcp_keepalive_time = 600
net.ipv4.tcp_keepalive_probes = 3
net.ipv4.tcp_keepalive_intvl =15
net.ipv4.tcp_retries1 = 3
net.ipv4.tcp_retries2 = 5
net.ipv4.tcp_fin_timeout = 10
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_max_tw_buckets = 60000
net.ipv4.tcp_max_orphans = 32768
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.tcp_mem = 94500000 915000000 927000000
net.ipv4.tcp_wmem = 4096 16384 13107200
net.ipv4.tcp_rmem = 4096 87380 17476000
net.ipv4.ip_local_port_range = 1024 65000
net.ipv4.ip_forward = 1
net.ipv4.route.gc_timeout = 100
net.core.somaxconn = 32768
net.core.netdev_max_backlog = 32768
net.nf_conntrack_max = 6553500
net.netfilter.nf_conntrack_max = 6553500
net.netfilter.nf_conntrack_tcp_timeout_established = 180
vm.overcommit_memory = 1
vm.swappiness = 1
fs.file-max = 1024000
EOF

#reload sysctl
/sbin/sysctl -p
sleep 1
}

# 设置UTF-8   LANG="zh_CN.UTF-8"
LANG_config(){
echo "LANG=\"en_US.UTF-8\"">/etc/locale.conf
source  /etc/locale.conf
}


#关闭SELINUX disable selinux
selinux_config(){
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
setenforce 0
sleep 1
}

#日志处理
log_config(){
setenforce 0
systemctl start systemd-journald
systemctl status systemd-journald
}


# 关闭防火墙
firewalld_config(){
/usr/bin/systemctl stop  firewalld.service
/usr/bin/systemctl disable  firewalld.service
}


# SSH配置优化 set sshd_config
sshd_config(){
if [ ! -f "/etc/ssh/sshd_config.bak" ]; then
    cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
fi

cat >/etc/ssh/sshd_config<<EOF
Port 22
AddressFamily inet
ListenAddress 0.0.0.0
Protocol 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
SyslogFacility AUTHPRIV
PermitRootLogin yes
MaxAuthTries 6
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile	.ssh/authorized_keys
PasswordAuthentication yes
ChallengeResponseAuthentication no
UsePAM yes
UseDNS no
X11Forwarding yes
UsePrivilegeSeparation sandbox
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS
Subsystem       sftp    /usr/libexec/openssh/sftp-server
EOF
/sbin/service sshd restart
}


# 关闭ipv6  disable the ipv6
ipv6_config(){
echo "NETWORKING_IPV6=no">/etc/sysconfig/network
echo 1 > /proc/sys/net/ipv6/conf/all/disable_ipv6
echo 1 > /proc/sys/net/ipv6/conf/default/disable_ipv6
echo "127.0.0.1   localhost   localhost.localdomain">/etc/hosts
#sed -i 's/IPV6INIT=yes/IPV6INIT=no/g' /etc/sysconfig/network-scripts/ifcfg-enp0s8


for line in $(ls -lh /etc/sysconfig/network-scripts/ifcfg-* | awk -F '[ ]+' '{print $9}')
do
if [ -f  $line ]
        then
        sed -i 's/IPV6INIT=yes/IPV6INIT=no/g' $line
                echo $i
fi
done
}


# 设置历史命令记录格式 history
history_config(){
export HISTFILESIZE=10000000
export HISTSIZE=1000000
export PROMPT_COMMAND="history -a"
export HISTTIMEFORMAT="%Y-%m-%d_%H:%M:%S "
##export HISTTIMEFORMAT="{\"TIME\":\"%F %T\",\"HOSTNAME\":\"\$HOSTNAME\",\"LI\":\"\$(who -u am i 2>/dev/null| awk '{print \$NF}'|sed -e 's/[()]//g')\",\"LU\":\"\$(who am i|awk '{print \$1}')\",\"NU\":\"\${USER}\",\"CMD\":\""
cat >>/etc/bashrc<<EOF
alias vi='vim'
HISTDIR='/var/log/command.log'
if [ ! -f \$HISTDIR ];then
touch \$HISTDIR
chmod 666 \$HISTDIR
fi
export HISTTIMEFORMAT="{\"TIME\":\"%F %T\",\"IP\":\"\$(ip a | grep -E '192.168|172' | head -1 | awk '{print \$2}' | cut -d/ -f1)\",\"LI\":\"\$(who -u am i 2>/dev/null| awk '{print \$NF}'|sed -e 's/[()]//g')\",\"LU\":\"\$(who am i|awk '{print \$1}')\",\"NU\":\"\${USER}\",\"CMD\":\""
export PROMPT_COMMAND='history 1|tail -1|sed "s/^[ ]\+[0-9]\+  //"|sed "s/$/\"}/">> /var/log/command.log'
EOF
source /etc/bashrc
}

# 服务优化设置
service_config(){
/usr/bin/systemctl enable NetworkManager-wait-online.service
/usr/bin/systemctl start NetworkManager-wait-online.service
/usr/bin/systemctl stop postfix.service
/usr/bin/systemctl disable postfix.service
chmod +x /etc/rc.local
chmod +x /etc/rc.d/rc.local
#ls -l /etc/rc.d/rc.local
}

# VIM设置
vim_config(){
cat > /root/.vimrc << EOF
set history=1000

EOF

#autocmd InsertLeave * se cul
#autocmd InsertLeave * se nocul
#set nu
#set bs=2
#syntax on
#set laststatus=2
#set tabstop=4
#set go=
#set ruler
#set showcmd
#set cmdheight=1
#hi CursorLine   cterm=NONE ctermbg=blue ctermfg=white guibg=blue guifg=white
#set hls
#set cursorline
#set ignorecase
#set hlsearch
#set incsearch
#set helplang=cn
}


# done
done_ok(){
touch /var/log/init-ok
cat << EOF
+-------------------------------------------------+
|               optimizer is done                 |
|   it's recommond to restart this server !       |
|             Please Reboot system                |
+-------------------------------------------------+
EOF
}

# main
main(){
    yum_update
    zone_time
    limits_config
    sysctl_config
    LANG_config
    selinux_config
    log_config
    firewalld_config
    sshd_config
    ipv6_config
    history_config
    service_config
    vim_config
    done_ok
}
main
```

docker-install.sh

```shell
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
curl -L "https://github.com/docker/compose/releases/download/1.28.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose version

# 安装Rar解压
wget https://www.rarlab.com/rar/rarlinux-x64-5.7.1.tar.gz
tar xvzf rarlinux-x64-5.7.1.tar.gz
mv rar /usr/local/
ln -s /usr/local/rar/unrar /usr/local/bin/unrar

# 安装应用
docker-compose up -d
# 查看应用列表
docker-compose ps -a

```



