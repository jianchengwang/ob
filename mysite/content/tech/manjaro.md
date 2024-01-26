---
title: manjaro
slug: manjaro
author: [jianchengwang]
date: 2019-09-20
excerpt: "之前使用Deepin的操作系统，虽然新版本稳定性跟易用性大大提高了，但软件源都比较旧，然后就安装下Manjaro Linux尝尝鲜，之前使用i3桌面，后面发现Teamview远程连接的时候，快捷键跟本机的经常冲突，所以还是使用DDE的桌面，懒得折腾。"
draft: false
tags: [linux]
---

之前使用 **Deepin** 的操作系统，虽然新版本稳定性跟易用性大大提高了，但软件源都比较旧，然后就安装下 **Manjaro Linux** 尝尝鲜，之前使用 **i3** 桌面，后面发现 **Teamview** 远程连接的时候，快捷键跟本机的经常冲突，所以还是使用 **DDE** 的桌面，懒得折腾。

[官网地址](https://manjaro.org/)

<!--more-->

### set mirrors

```shell
## build china mirrors
sudo pacman-mirrors -i -c China -m rank

## add ustc mirros

sudo vim /etc/pacman.conf 
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch

sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring

```

### pacman & yay

```shell
## pacman
pacman -S package_name1 package_name2 # add soft
pacman -R package_name # remove soft
pacman -Syu # update system and soft
pacman -Ss string1 string2 # search

## yay
yay <Search Term>yay <Search Term>
        Present package-installation selection menu.

yay -Ps
        Print system statistics.

yay -Yc
        Clean unneeded dependencies.

yay -G <AUR Package>
        Download PKGBUILD from ABS or AUR.

yay -Y --gendb
        Generate development package database used for devel update.

yay -Syu --devel --timeupdate
        Perform system upgrade, but also check for development package updates and use
        PKGBUILD modification time (not version number) to determine update.

        Present package-installation selection menu.

yay -Ps
        Print system statistics.

yay -Yc
        Clean unneeded dependencies.

yay -G <AUR Package>
        Download PKGBUILD from ABS or AUR.

yay -Y --gendb
        Generate development package database used for devel update.

yay -Syu --devel --timeupdate
        Perform system upgrade, but also check for development package updates and 			use PKGBUILD modification time (not version number) to determine update.


```

### config & lib

```shell
sudo pacman -S gconf
yay -S lib32-glibc
yay -S glibc 
```

###  fcit

```shell
## add fcitx
sudo pacman -S fcitx-im
sudo pacman -S fcitx-configtool

## because i use dde desktop, so i edit .profile 
vim ~/.profile
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"


## add you like fcitx inputmode
sudo pacman -S fcitx-sogoupinyin
sudo pacman -S fcitx-googlepinyin
sudo pacman -S fcitx-sunpinyin
sudo pacman -S fcitx-cloudpinyin

```

### zsh

```shell
sudo pacman -S git
sudo pacman -S zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
chsh -s /bin/zsh

sudo pacman -S gconf
```

### environment variables

```shell
vim ~/.profile
export JAVA_HOME=/opt/program/jdk1.8.0_151
export JRE_HOME=${JAVA_HOME}/jre   
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib

export MAVEN_HOME=/opt/server/apache-maven-3.5.2
export NODE_HOME=/opt/program/node-v8.12.0-linux-x64

export PATH=$PATH:${JAVA_HOME}/bin:${NODE_HOME}/bin:${MAVEN_HOME}/bin
```

### mount & fstab

```shell
sudo mount /dev/sdb2 /opt

vim /etc/fstab

/dev/sdb2                                /opt           ext4    defaults

/dev/sda6                               /home/wjc/play  ext4    defaults
/dev/sda7                               /home/wjc/ext   ext4    defaults
```

### install soft

```shell
sudo pacman -S python-pip
sudo pacman -S curl
sudo pip install uncurl
pip install youtube-dl

sudo pacman -S net-tools
sudo pacman -S make

sudo pacman -S autojump
.zshrc plugins=(git autojump)
sudo pacman -S screenfetch
sudo pacman -S speedtest-cli
sudo pacman -S redshift

sudo pacman -S vim
sudo pacman -S wiznote
sudo pacman -S typora
sudo pacman -S foxitreader
sudo pacman -S code
sudo pacman -S sublime-text-dev

sudo pacman -S wps-office ttf-wps-fonts

sudo pacman -S vlc
sudo pacman -S netease-cloud-music
sudo pacman -S spotify
235170
sudo pacman -S google-chrome
sudo pacman -S filezilla
sudo pacman -S teamviewer
systemctl enable teamviewerd
systemctl start teamviewerd
sudo pacman -S shadowsocks-qt5
sudo pacman -S proxychains-ng
sudo vim /etc/proxychains.conf
socks5 127.0.0.1 1080
proxychains wget https://wwww.google.com

sudo pip3 install genpac
genpac --format=pac --pac-proxy="SOCKS5 127.0.0.1:1080" --gfwlist-proxy="SOCKS5 127.0.0.1:1080" -o autoproxy.pac --gfwlist-url="https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt"

sudo pip3 install tldr
tldr curl

sudo pacman -S deepin.com.qq.office
sudo pacman -S electronic-wechat-git
yay -S deepin.com.thunderspeed

## install win10 font
sudo pacman -U ttf-ms-win10-zh_cn-10.0.17763.1-1-any.pkg .tar

## vmware
sudo systemctl start vmware-networks.service
sudo modprobe vmnet && sudo vmware-networks --start

## bumblebee
sudo pacman -S virtualgl lib32-virtualgl lib32-primus primus
sudo mhwd -f -i pci video-hybrid-intel-nvidia-bumblebee
sudo systemctl enable bumblebeed

sudo gpasswd -a wjc bumblebee
sudo reboot

glxgears
optirun glxgears

## dota2
vblank_mode=0 primusrun %command% -perfectworld -console -cursor_scale_percent 100 -language schinese

## mariadb
sudo pacman -S mariadb mariadb-clients
sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
mysqladmin -u root password '123456' 
mysql -uroot -p 123456
sudo systemctl enable mysqld
```