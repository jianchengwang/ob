---
title: mac-m1
createdAt: 2021-03-02
categories: 
- mac
- env
tags: 
- mac
---

 最近入手了mac m1 air乞丐版，主要看中续航，轻量，可以作为外出的便携本，之前没用过mac系统，这里就配置基础开发环境，简单做个笔记，

<!--more-->

## ssh

生成ssh密钥，后面可以免密登录远程服务器或者提交git代码，

```shell
cd .ssh
ssh-keygen -t rsa -C jiancheng_wang@yahoo.com
ssh-copy-id -p 22 root@122.51.87.176

# 复制公钥到git服务器
cat ~/.ssh/id_rsa.pub
```

## homebrew

mac的包管理工具，

```shell
cd /opt # 切换到 /opt 目录
mkdir homebrew # 创建 homebrew 目录
sudo chown -R $(whoami) /opt/homebrew 
curl -L https://github.com/Homebrew/brew/tarball/master | tar xz --strip 1 -C homebrew

vim .zshrc
path=('/opt/homebrew/bin' $path)
export PATH
```

## terminal

这边使用item2，官网下载即可，https://iterm2.com/

```shell
# 安装oh-my-zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# oh-my-zsh plugins
# Syntax Highlighting Plugin
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
# ZSH-AutoSuggestion Plugin
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
# 修改 ~/.zshrc 以使用 plugins
vim ~/.zshrc
plugins=(
git
zsh-syntax-highlighting
zsh-autosuggestions
)
```

## switchhosts

https://github.com/oldj/SwitchHosts/

本地作为开发环境的话，可以方便切换hosts，

比如dns被污染了，可以上https://www.ipaddress.com/查询对应域名，配置下即可，

```shell
199.232.96.133 raw.github.com
199.232.68.133 raw.githubusercontent.com
199.232.68.133 user-images.githubusercontent.com
199.232.68.133 avatars2.githubusercontent.com
199.232.68.133 avatars1.githubusercontent.com
```

## proxychains-ng

<!-- **qv2ray**

https://github.com/Qv2ray/Qv2ray/

科学上网，基本程序员都必备吧，当然也可以使用[clashX](https://github.com/yichengchen/clashX/releases)

文档参考 https://bluedoc.io/agentneo/docs/mk22 -->

**proxychains-ng**

https://github.com/rofl0r/proxychains-ng

方便实现终端代理，

## code

[zulu-jdk](https://www.azul.com/downloads/zulu-community/?os=macos&architecture=arm-64-bit&package=jdk)

Jetbrains 全家桶基本都已适配m1芯片

Vscode Insider 已适配m1芯片

## typora

https://typora.io/

简洁的markdown编辑器，结合[picgo](https://github.com/Molunerfinn/PicGo)可以方便的上传图片到云存储，









