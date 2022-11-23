---
title: window-terminal
slug: window-terminal
author: [jianchengwang]
date: 2021-06-28
excerpt: "**window terminal** 是win10提供的终端软件，可以在microsoft store下载"
draft: false
tags: 
- tool
---

**window terminal** 是win10提供的终端软件，可以在microsoft store下载

### 安装 posh-git 和 oh-my-posh 这两个模块

```
Install-Module posh-git -Scope CurrentUser 
Install-Module oh-my-posh -Scope CurrentUser
```

### 让 PowerShell 主题配置生效

#### 新增（或修改）你的 PowerShell 配置文件

```powershell
# 如果之前没有配置文件，就新建一个 PowerShell 配置文件
if (!(Test-Path -Path $PROFILE )) { New-Item -Type File -Path $PROFILE -Force }
# 用记事本打开配置文件
notepad  $PROFILE
```

#### 在其中添加下面的内容

```powershell
Import-Module posh-git 
Import-Module oh-my-posh 
Set-PoshPrompt Paradox
```

其中最后一句 `Set-PoshPrompt <主题名>` 就是配置主题的命令。

**值得注意**：如果你发现后面的日期显示出现了凌乱的现象（比如本该在同一行显示的字符却跑到了下一行），多半是因为显示了中文。目前很多终端都不能正常的显示中文或 CJK 字符（即：Double-width character），所以你可以通过下面这个命令将 PowerShell 的环境设置为 en-US 的英文环境：

```powershell
Set-Culture en-US
```

一般来说，PowerShell 的用户配置文件在 `C:\Users\<用户名>\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`，这个也就是刚刚安装过程中生成的文件，接下来定制的过程，就修改这个文件来配置即可。

#### PowerShell 提示“无法加载文件，因为在此系统上禁止运行脚本”的解决方法

以管理员身份打开PowerShell 输入

```powershell
set-executionpolicy remotesigned
```

## 主题推荐

使用某个主题很简单，下面这个命令就可以让我们预览某个主题：

```
Set-PoshPrompt <主题名>
```

### 定制自己的主题

`oh-my-posh` 是相对比较完善的 PowerShell 主题配置引擎，因此我们也可以魔改某个主题，来让它达到我们想要的效果，甚至自己写一个主题配置也可以。在 `oh-my-posh` 的主题文件夹 `C:\Users\<用户名>\Documents\WindowsPowerShell\Modules\oh-my-posh\<版本号>\Themes` 下新建一个 `myTheme.psm1`，之后按照其他主题的写法进行修改就可以了。使用 `Set-PoshPrompt myTheme` 这个命令来让你的自定义主题生效。

