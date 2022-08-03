---
title: vps
createdAt: 2019-04-09
categories: 
- play
- gfw
tags: 
- vps
- gfw
draft: true
---

VPS厂商很多，

如果使用**vultr**厂商注册新用户的话，请戳，[vultr推广连接](https://www.vultr.com/?ref=7567014)

如果使用阿里云的话，请戳， [阿里云大使](https://promotion.aliyun.com/ntms/yunparter/invite.html?userCode=um4bcafi)

以下是vultr的官方测速服务器，你可以进行下载测试，因为运营商或是位置的不同，不同位置速度都有差异，自行选择最适合自己的。

<!--more-->

| Location 地理位置                           | Hostname 官方测试服务器ip | Download Test File 下载测试文件                              |
| ------------------------------------------- | ------------------------- | ------------------------------------------------------------ |
| (Asia)Tokyo, Japan[日本 东京]               | hnd-jp-ping.vultr.com     | [100Mb](http://hnd-jp-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://hnd-jp-ping.vultr.com/vultr.com.1000MB.bin) |
| Singapore[新加坡]                           | sgp-ping.vultr.com        | [100Mb](http://sgp-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://sgp-ping.vultr.com/vultr.com.1000MB.bin) |
| (AU) Sydney, Australia[悉尼]                | syd-au-ping.vultr.com     | [100Mb](http://syd-au-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://syd-au-ping.vultr.com/vultr.com.1000MB.bin) |
| (EU) Frankfurt, DE[德国 法兰克福]           | fra-de-ping.vultr.com     | [100Mb](http://fra-de-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://fra-de-ping.vultr.com/vultr.com.1000MB.bin) |
| (EU) Amsterdam, NL[荷兰 阿姆斯特丹]         | ams-nl-ping.vultr.com     | [100Mb](http://ams-nl-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://ams-nl-ping.vultr.com/vultr.com.1000MB.bin) |
| (EU) London, UK[英国 伦敦]                  | lon-gb-ping.vultr.com     | [100Mb](http://lon-gb-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://lon-gb-ping.vultr.com/vultr.com.1000MB.bin) |
| (EU) Paris, France[法国 巴黎]               | par-fr-ping.vultr.com     | [100Mb](http://par-fr-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://par-fr-ping.vultr.com/vultr.com.1000MB.bin) |
| Seattle, Washington[美东 华盛顿州 西雅图]   | wa-us-ping.vultr.com      | [100Mb](http://wa-us-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://wa-us-ping.vultr.com/vultr.com.1000MB.bin) |
| Silicon Valley, Ca[美西 加州 硅谷]          | sjo-ca-us-ping.vultr.com  | [100Mb](http://sjo-ca-us-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://sjo-ca-us-ping.vultr.com/vultr.com.1000MB.bin) |
| Los Angeles, Ca[美西 加州 洛杉矶**(推荐)**] | lax-ca-us-ping.vultr.com  | [100Mb](http://lax-ca-us-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://lax-ca-us-ping.vultr.com/vultr.com.1000MB.bin) |
| Chicago, Illinois[美东 芝加哥]              | il-us-ping.vultr.com      | [100Mb](http://il-us-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://il-us-ping.vultr.com/vultr.com.1000MB.bin) |
| Dallas, Texas[美中 德克萨斯州 达拉斯]       | tx-us-ping.vultr.com      | [100Mb](http://tx-us-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://tx-us-ping.vultr.com/vultr.com.1000MB.bin) |
| New York / New Jersey[美东 新泽西]          | nj-us-ping.vultr.com      | [100Mb](http://nj-us-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://nj-us-ping.vultr.com/vultr.com.1000MB.bin) |
| Atlanta, Georgiaa[美东 乔治亚州 亚特兰大]   | ga-us-ping.vultr.com      | [100Mb](http://ga-us-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://ga-us-ping.vultr.com/vultr.com.1000MB.bin) |
| Miami, Florida[美东 佛罗里达州 迈阿密]      | fl-us-ping.vultr.com      | [100Mb](http://fl-us-ping.vultr.com/vultr.com.100MB.bin) [1000Mb](http://fl-us-ping.vultr.com/vultr.com.1000MB.bin) |

个人选择的服务器系统是centos7，所以以下shell命令都是基于centos7。

#### 基本配置

创建一个用户进行操作，当然其实也没什么必要，因为现在基本vps都可以创建镜像快照可以进行恢复操作。所以下面还是都是使用root用户进行操作。

```shell
# 创建用户
useradd wjc
passwd wjc

# 授予sudo权限
chmod u+w /etc/sudoers
vi /etc/sudoers
wjc     ALL=(ALL)       ALL

# 添加ssh公钥到vps主机，以实现免密登录
ssh-copy-id user@vpsip
```

#### SSR

身为一名程序猿，经常需要用到谷歌搜索引擎，所以来一瓶酸酸乳是很有必要的，网上很多一键安装脚本，比如[秋水逸冰](https://shadowsocks.be/9.html) 或是通过[kcptun](https://github.com/xtaci/kcptun)

现在GFW升级，如果国内SSH端口22显示关闭，说明这个ip已经被封了，只能换一个IP地址了

[全国ping测速网页](https://tools.ipip.net/ping.php)

[国内端口查看](http://coolaf.com/tool/port)

[国外端口查看](https://www.yougetsignal.com/tools/open-ports/)

```shell
# 1.ssh 到vps服务器
ssh  -o StrictHostKeyChecking=no root@vpsip

# 2.测试服务器性能
wget -qO- git.io/superbench.sh | bash

# 3.一键安装ssr脚本
wget --no-check-certificate https://freed.ga/github/shadowsocksR.sh;bash shadowsocksR.sh

vi /etc/shadowsocks.json

/etc/init.d/shadowsocks start
/etc/init.d/shadowsocks stop
/etc/init.d/shadowsocks restart
/etc/init.d/shadowsocks status

# 或安装kcptun for ssr一键脚本
wget --no-check-certificate -O kcptun_for_ss_ssr.sh https://git.io/fN2EO
chmod 700 kcptun_for_ss_ssr.sh
./kcptun_for_ss_ssr.sh install

# 4.一键开启bbr
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh

sysctl net.ipv4.tcp_available_congestion_control
# 返回值一般为：
net.ipv4.tcp_available_congestion_control = bbr cubic reno
或者为：
net.ipv4.tcp_available_congestion_control = reno cubic bbr

# 或锐速
# 更换内核yum -y install wget screen   // For CentOS / Redhat
wget http://mirrors.linuxeye.com/lnmp-full.tar.gz   // Contains the source code
tar xzf lnmp-full.tar.gz
cd lnmp    // If you need to modify the directory (installation, data storage, Nginx logs), modify options.conf file
screen -S lnmp    // if network interruption, you can execute the command `screen -r lnmp` reconnect install window
./install.sh
wget-N --no-check-certificate https://freed.ga/kernel/ruisu.sh && bashruisu.sh
# 一键安装锐速脚本
wget -N --no-check-certificatehttps://github.com/91yun/serverspeeder/raw/master/serverspeeder.sh &&bash serverspeeder.sh
# 备用脚本
wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/serverspeeder/master/serverspeeder-all.sh&& bash serverspeeder-all.sh

# 如果出现重启出现文件系统变为 read-only file system，无法执行写入操作
mount -o remount rw /
```

#### Trojan

因为`ssr`很多不能用了，后面安装了`trojan`，这里备注下。

首先有个域名，然后指向`VPS`地址，然后申请证书，可以到[freessl](https://freessl.cn/)申请免费证书，证书配置好了可以访问下`https`域名的地址看是否配置成功。

然后安装`trojan服务器`我使用的一键安装脚本

```shell
wget -N --no-check-certificate -q -O trojan_install.sh "https://raw.githubusercontent.com/V2RaySSR/Trojan/master/trojan_install.sh" && chmod +x trojan_install.sh && bash trojan_install.sh
```

设置开机启动，服务相关命令

```shell
systemctl enable trojan
systemctl start trojan
```

相关配置，如果需要可以修改trojan安装目录下的配置文件，`config.json`， `server.json`一般默认即可，`server.json` 里有设置密码，等下客户端连接需要用到。

然后安装`trojan客户端` [点击下载](https://github.com/trojan-gfw/trojan/releases)  修改客户端的`config.json` ，一般修改`remote_addr`为你指向`vps`的域名，`remote_addr`也可以设置成`vps`的`ip`，这时`verify`和`verify_hostname`需要设置成`false`， `password`就是上面说的`taojan服务器` `server.json` 配置文件设置的密码  

```json
"run_type": "client",
    "local_addr": "127.0.0.1",
    "local_port": 1080,
    "remote_addr": "",
    "remote_port": 443,
    "password": [
        "xxx"
    ],
```

然后启动即可，启动不起来，安装下安装包里面的依赖

然后设置系统代理了，这个很多种，我这边是安装[v2rayN客户端](https://github.com/2dust/v2rayN/releases)

启动`v2ray客户端` 添加个`Socks`服务器，本地默认地址就是`127.0.0.1` ，本地默认端口就是`1080`，就ojbk了。

参考链接:  https://tlanyan.me/trojan-tutorial/

#### Aria2 + Google Driver

##### 安装aria2

```shell
# 安装aria2
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/aria2.sh && chmod +x aria2.sh && bash aria2.sh
#备用地址
wget -N --no-check-certificate https://www.moerats.com/usr/shell/Aria2/aria2.sh && chmod +x aria2.sh && bash aria2.sh
# 配置aira2
vim /root/.aria2/aria2.conf
# 重新启动
service aria2 resatrt

# 创建 rcloneupload.sh

```

##### 安装rclone

```shell
curl https://rclone.org/install.sh | sudo bash
or 
wget https://www.moerats.com/usr/shell/rclone_debian.sh && bash rclone_debian.sh

# 配置
rclone config
# 挂载
yum install fuse
rclone mkdir /root/backup
rclone mount gdrive:backup /root/backup --allow-other --allow-non-empty --vfs-cache-mode writes &
# 卸载
fusermount -qzu /root/backup
# 上传
rclone -vv copy /root/backup gdrive:backup
# 下载
rclone -vv copy gdrive:backup /root/backup

```

##### 创建 rcloneupload.sh

```shell
#!/bin/bash

GID="$1";
FileNum="$2";
File="$3";
MinSize="5"  #限制最低上传大小，默认5k
MaxSize="157286400"  #限制最高文件大小(单位k)，默认15G
RemoteDIR="/root/wjcgd/";  #rclone挂载的本地文件夹，最后面保留/
LocalDIR="/root/download/";  #Aria2下载目录，最后面保留/

if [[ -z $(echo "$FileNum" |grep -o '[0-9]*' |head -n1) ]]; then FileNum='0'; fi
if [[ "$FileNum" -le '0' ]]; then exit 0; fi
if [[ "$#" != '3' ]]; then exit 0; fi

function LoadFile(){
  IFS_BAK=$IFS
  IFS=$'\n'
  if [[ ! -d "$LocalDIR" ]]; then return; fi
  if [[ -e "$File" ]]; then
    FileLoad="${File/#$LocalDIR}"
    while true
      do
        if [[ "$FileLoad" == '/' ]]; then return; fi
        echo "$FileLoad" |grep -q '/';
        if [[ "$?" == "0" ]]; then
          FileLoad=$(dirname "$FileLoad");
        else
          break;
        fi;
      done;
    if [[ "$FileLoad" == "$LocalDIR" ]]; then return; fi
    EXEC="$(command -v mv)"
    if [[ -z "$EXEC" ]]; then return; fi
    Option=" -f";
    cd "$LocalDIR";
    if [[ -e "$FileLoad" ]]; then
      ItemSize=$(du -s "$FileLoad" |cut -f1 |grep -o '[0-9]*' |head -n1)
      if [[ -z "$ItemSize" ]]; then return; fi
      if [[ "$ItemSize" -le "$MinSize" ]]; then
        echo -ne "\033[33m$FileLoad \033[0mtoo small to spik.\n";
        return;
      fi
      if [[ "$ItemSize" -ge "$MaxSize" ]]; then
        echo -ne "\033[33m$FileLoad \033[0mtoo large to spik.\n";
        return;
      fi
      eval "${EXEC}${Option}" \'"${FileLoad}"\' "${RemoteDIR}";
    fi
  fi
  IFS=$IFS_BAK
}
LoadFile;
```

```shell
chmod +x rcloneupload.sh
vim /root/.aria2/aria2.conf
on-download-complete=/root/rcloneupload.sh
```

