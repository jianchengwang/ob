---
title: idea
slug: idea
author: [jianchengwang]
date: 2021-02-08
excerpt: "**IntelliJ IDEA**，java的集成开发环境，这边记录下常用的快捷键，以及注册码等，更多信息可以查看官网"
draft: false
tags: 
- tool
---

**IntelliJ IDEA**，java的集成开发环境，这边记录下常用的快捷键，以及注册码等，更多信息可以查看官网，[idea](https://www.jetbrains.com/idea/)

## 快捷键

### 查找

| 快捷键                 | 介绍                         |
| :--------------------- | ---------------------------- |
| Ctrl + F               | 在当前文件进行文本查找       |
| Ctrl + R               | 在当前文件进行文本替换       |
| Shift + Ctrl + F       | 在项目进行文本查找           |
| Shift + Ctrl + R       | 在项目进行文本替换           |
| **Shift  + Shift**     | 快速搜索                     |
| **Ctrl + N**           | 查找class                    |
| Ctrl + Shift + N       | 查找文件                     |
| Ctrl + Shift + Alt + N | 查找symbol（查找某个方法名） |

### 跳转切换

| 快捷键                | 介绍                  |
| --------------------- | --------------------- |
| Ctrl + E              | 最近文件              |
| Ctrl + Tab            | 切换文件              |
| **Ctrl  + Alt + ←/→** | 跳转历史光标所在处    |
| Alt + ←/→ 方向键      | 切换子tab             |
| **Ctrl + G**          | go to（跳转指定行号） |

### 编码相关

| 快捷键                       | 介绍                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| Ctrl + W                     | 快速选中                                                     |
| **(Shift + Ctrl) + Alt + J** | 快速选中同文本                                               |
| Ctrl + C/Ctrl + X/Ctrl + D   | 快速复制或剪切                                               |
| 多行选中 Tab / Shift  + Tab  | tab                                                          |
| Ctrl + Y                     | 删除整行                                                     |
| 滚轮点击变量/方法/类         | 快速进入变量/方法/类的定义处                                 |
| Shift + 点击Tab              | 快速关闭tab                                                  |
| Ctrl + Z 、Ctrl + Shift + Z  | 后悔药，撤销/取消撤销                                        |
| Ctrl + Shift + enter         | 自动收尾，代码自动补全                                       |
| **Alt + enter**              | IntelliJ IDEA 根据光标所在问题，提供快速修复选择，光标放在的位置不同提示的结果也不同 |
| **Alt + ↑/↓**                | 方法快速跳转                                                 |
| F2                           | 跳转到下一个高亮错误 或 警告位置                             |
| Alt + Insert                 | 代码自动生成，如生成对象的 set / get 方法，构造函数，toString() 等 |
| **Ctrl + Shift + L**         | 格式化代码                                                   |
| **Shift + F6**               | 快速修改方法名、变量名、文件名、类名等                       |
| **Ctrl + F6**                | 快速修改方法签名                                             |

### 代码阅读相关

| 快捷键                         | 介绍                               |
| ------------------------------ | ---------------------------------- |
| **Ctrl + P**                   | 方法参数提示显示                   |
| Ctrl + Shift + i               | 就可以在当前类里再弹出一个窗口出来 |
| Alt + F7                       | 可以列出变量在哪些地方被使用了     |
| **光标在子类接口名，Ctrl + u** | 跳到父类接口                       |
| Alt + F1 + 1， esc             |                                    |
| **(Shift) + Ctrl + +/-**       | 代码块折叠                         |
| Ctrl + Shift + ←/→             | 移动窗口分割线                     |
| **Ctrl  + (Alt) + B**          | 跳转方法定义/实现                  |
| **Ctrl  + H**                  | 类的层级关系                       |
| Ctrl  + F12                    | Show Members 类成员快速显示        |
| **Structure**                  | 显示类的结构                       |
| **Diagrams**                   | 显示类继承跟接口实现               |

### 版本管理相关

| 快捷键       | 介绍             |
| ------------ | ---------------- |
| Ctrl + D     | Show Diff        |
| (Shift) + F7 | （上）下一处修改 |

> 更多快捷键请参考 [github.com/judasn/Inte…](https://github.com/judasn/IntelliJ-IDEA-Tutorial/blob/master/keymap-introduce.md)
>
>  **mac os** 快捷键请参考 [github.com/judasn/Inte…](https://github.com/judasn/IntelliJ-IDEA-Tutorial/blob/master/keymap-win-mac.md)

## 调试

一般最常使用行断点，

- step over: 一行一行往下执行
- step into：进入方法内部，不会进入java官方类库
- force step into：强制进入方法内部
- step out：跳出方法内部，此方法执行完毕
- drop frame：回退到上一个断点，
- return to cursor：跳到鼠标指针所在的行断点，

还有条件断点，跟force return也比较常用，

## 插件

我目前就安装了以下插件，

- Free Mybatis plugin
- Lombok
- Translation
- Statistic
- jclasslib

## 扩展工具

Tools-External-Tools

这里加了两个反编译的命令，如果需要更详细的也可以使用`javap -c`，

`javap -p`

Programe: `/usr/bin/javap`

Arguments: `-p $FileClass$`

Workdir: `$OutputPath$`

`javap -v / java -verbose`

Programe: `/usr/bin/javap`

Arguments: `-v $FileClass$`

Workdir: `$OutputPath$`



## 注册码

参考 [https://jetbra.in/s](https://jetbra.in/s)

网上也一堆魔改的，这个建议有资金的可以购买正版，或者根据开源项目申请，门槛不会很高。