---
title: ssh
slug: ssh
author: [jianchengwang]
date: 2019-11-10
excerpt: "SSH即Secure Shell，是一个提供数据通信安全、远程登录、远程指令执行等功能的安全网络协议，由芬兰赫尔辛基大学研究员Tatu Ylönen，于1995年提出，其目的是用于替代非安全的Telnet、rsh、rexec等远程Shell协议。之后SSH发展了两个大版本SSH-1和SSH-2"
draft: false
tags: [network]
---

**SSH** 即 **Secure Shell**，是一个提供数据通信安全、远程登录、远程指令执行等功能的安全网络协议，由芬兰赫尔辛基大学研究员Tatu Ylönen，于1995年提出，其目的是用于替代非安全的Telnet、rsh、rexec等远程Shell协议。之后SSH发展了两个大版本SSH-1和SSH-2。

作为一名程序员或是运维人员，一般都会遇到 **SSH** 登录，或是 **Git** 提交等事物，每次都手动输入密码显然不是一个明智的选择，虽然有很多工具可以帮我们保存密码，但是命令才是核心，所以这篇文章简单记录下，SSH 的相关原理命令和如何实现免密登录。

### 基本概念

#### 加密方法

现在一般的加密方法有两种，

**单钥加密/对称加密** (private key cryptography)，

简而言之就一个密钥，所以密钥一旦泄露就，密码也被破解了。一般用来加密大量数据时候使用，算法公开，计算量少，加密速度快，加密效率高，主要算法有 **DES**,  **AES** ，**3DES**等

  **双钥加密/非对称加密** (public key cryptography)

有两把密钥，私钥可以解密公钥加密的数据，公钥可以解密私钥加密的数据，一般公钥可以给任何人，只要私钥不泄露，一般都是安全的，主要算法有 **RSA**，这种加密方式比较复杂，加密解密速度慢，一般只适用于对少量数据进行加密，比如用户账号密码等。

#### 数据一致性

数据一致性说得是如何保证一段数据在传输的过程中没有遗漏、破坏或者修改过。一般来说，目前流行的做法是对数据进行hash，
得到的hash值和数据一起传输，然后在收到数据的时候也对数据进行hash，将得到的hash值和传输过来的hash值进行比对，
如果是不一样的，说明数据已经被修改过；如果是一样的，则说明极有可能是完整的。

目前流行的hash算法有 [**MD5**](https://en.wikipedia.org/wiki/MD5) 和 [**SHA-1**](http://link.zhihu.com/?target=http%3A//en.wikipedia.org/wiki/Sha1) 算法。

#### 身份验证

身份验证说的是，判断一个人或者机器是不是就是你想要联系的。也就是说如果A想要和B通信，一般来说开始的时候会交换一些数据，
A怎么可以判断发送回来的数据就真的是B发送的呢？现实中有很多方法可以假冒一个机器。

在SSH里面，这主要是通过公钥来完成的。首先客户端会有一个公钥列表，保存的是它信任的机器上面的公钥。
在开始SSH连接之后，服务器会发送过来一个公钥，然后客户端就会进行查找，如果这个公钥在这个列表里面，就说明这个机器是真的服务器。

#### 数字签名/数字证书

简而言之，就是 **发送者** 根据 **HASH** 函数对发送信件的内容形成一个 **摘要 (digest)**，然后通过私钥加密，生成 **数字签名(signature)** 然后加密后的信件后面一起发送给**接收方 **，

**接收方** 收到之后通过 **发送方** 给的公钥进行解密得到信件的摘要，再通过对解密后的信件内容进行再次 HASH 函数得到摘要，如果两个摘要一致，则说明信件没有被修改过。

但是接收方怎么确定公钥是发送方给的呢，这就需要 **证书中心 (certificate authority，简称CA)** 给公钥进行认证了。证书中心用自己的私钥，对发送方的公钥和一些相关信息一起加密，生成 **数字证书 (Digital Certificate)**。

这个建议看下

[阮一峰的翻译数字签名是什么](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)

[翻译原文](http://www.youdzone.com/signature.html)

### SSH 认证流程

SSH 不像https一样，有一个CA提供数字证书认证，SSH 的认证有以下两种形式：

#### 基于口令

就是输入账号跟密码了，这个一般第一次会提示

```shell
The authenticity of host 'host (12.18.429.21)' can't be established.
RSA key fingerprint is 98:2e:d7:e0:le:​9f:ac:67:28:c2:42:2d:37:16:58:4d.
Are you sure you want to continue connecting (yes/no)?
```

这段话的意思是，无法确认host主机的真实性，只知道它的公钥指纹，问你还想继续连接吗？yes的话，远程主机的公钥会被保存在$HOME/.ssh/know_hosts里

```shell
ssh -p 22 user@host 

```

#### 公钥认证/免密登录

```shell
# 生成公钥跟私钥，会在$HOME/.ssh 目录生成id_rsa  id_rsa.pub两个密钥文件
ssh-keygen -t rsa // 默认生成2048位的RSA密钥
ssh-keygen -b 4096 -t rsa // 生成4096位的RSA密钥

# 将本机公钥文件写入到远程主机.ssh/authorized_keys 文件
cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"

# 等价于
ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub

# linux 可以使用ssh-copy-id, 不过他会将本地所有公钥文件上传到远程主机
ssh-copy-id user@host

# 或是
cat id_rsa.pub>>authorized_keys
scp -r authorized_keys user@host:~/.ssh

# 如果还是不能免密登录，则估计是权限问题，设置远程主机的文件权限
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

# 如果还是不行估计是远程主机ssh的配置文件是否有去掉注释
vim /etc/ssh/sshd_config
　　RSAAuthentication yes
　　PubkeyAuthentication yes
　　AuthorizedKeysFile .ssh/authorized_keys
　　
service ssh restart // ubuntu 体系
/etc/init.d/ssh restart // debian 体系


# 配置本机 $HOME/.ssh/config 文件
vim ~/.ssh/config
Host yourserver
  HostName 192.168.11.22
  Port 22
  User username
  IdentityFile ~/.ssh/id_rsa

# 现在就可以免密登录了
ssh youorserver
```

### 扩展实例

#### SSH 数据传输

```shell

# $HOME/src/目录下面的所有文件，复制到远程主机的$HOME/src/目录
cd && tar czv src | ssh user@host 'tar xz'

# 远程主机$HOME/src/目录下面的所有文件，复制到用户的当前目录
ssh user@host 'tar cz src' | tar xzv

# 查看远程主机是否运行进程httpd
ssh user@host 'ps ax | grep [h]ttpd'

```

#### SCP 拷贝

**scp** 是 **secure copy** 的简写，用于在Linux下进行远程拷贝文件的命令，和它类似的命令有cp，不过cp只是在本机进行拷贝不能跨服务器，而且scp传输是加密的。

```shell
-1 强制scp命令使用协议ssh1
-2 强制scp命令使用协议ssh2
-4 强制scp命令只使用IPv4寻址
-6 强制scp命令只使用IPv6寻址
-B 使用批处理模式（传输过程中不询问传输口令或短语）
-C 允许压缩。（将-C标志传递给ssh，从而打开压缩功能）
-p 留原文件的修改时间，访问时间和访问权限。
-q 不显示传输进度条。
-r 递归复制整个目录。
-v 详细方式显示输出。scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。
-c cipher 以cipher将数据传输进行加密，这个选项将直接传递给ssh。
-F ssh_config 指定一个替代的ssh配置文件，此参数直接传递给ssh。
-i identity_file 从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh。
-l limit 限定用户所能使用的带宽，以Kbit/s为单位。
-o ssh_option 如果习惯于使用ssh_config(5)中的参数传递方式，
-P port 注意是大写的P, port是指定数据传输用到的端口号
```

```shell
scp source_file_path destination_file_path

# 本地复制远程文件
scp root@www.test.com:/val/test/test.tar.gz /val/test/test.tar.gz

# 远程复制本地文件
scp /val/test.tar.gz root@www.test.com:/val/test.tar.gz

# 本地复制远程目录
scp -r root@www.test.com:/val/test/ /val/test/

# 远程复制本地目录
scp -r ./ubuntu_env/ root@192.168.0.111:/home/pipi

```

#### 端口操作

```shell
# SSH会建立一个socket，去监听本地的8080端口。一旦有数据传向那个端口，就自动把它转移到SSH连接上面，发往远程主机
ssh -D 8080 user@host

# 假定host1是本地主机，host2是远程主机。由于种种原因，这两台主机之间无法连通。但是，另外还有一台host3，可以同时连通前面两台主机。因此，很自然的想法就是，通过host3，将host1连上host2。命令中的L参数一共接受三个值，分别是"本地端口:目标主机:目标主机端口"，它们之间用冒号分隔。这条命令的意思，就是指定SSH绑定本地端口2121，然后指定host3将所有的数据，转发到目标主机host2的21端口（假定host2运行FTP，默认端口为21）。
ssh -L 2121:host2:21 host3

# 远程端口转发 
ssh -R 2121:host2:21 host1
# R参数也是接受三个值，分别是"远程主机端口:目标主机:目标主机端口"。这条命令的意思，就是让host1监听它自己的2121端口，然后将所有数据经由host3，转发到host2的21端口
```

### 相关链接

[ssh的用法及命令](https://blog.csdn.net/pipisorry/article/details/52269785)

[SSH的原理及应用](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)

[图解SSH原理](https://www.jianshu.com/p/33461b619d53)

[SSH的免密登录怎么玩](https://zhuanlan.zhihu.com/p/28423720)

