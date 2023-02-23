---
title: geek-redis
slug: geek-redis
author: [jianchengwang]
date: 2020-03-10
excerpt: "最近在喵极客时间的redis的核心技术与实战，这里简单做个笔记"
draft: true
tags: [middleware]
---

## 数据结构

### 底层实现

Redis 键值对中值的数据类型，也就是数据的保存形式。而这里，我们说的数据结构，是要去看看它们的底层实现。简单来说，底层数据结构一共有 6 种，分别是简单动态字符串、双向链表、压缩列表、哈希表、跳表和整数数组。

![](images/geek-redis/redis-datastruct.png)

### 键值组织

![](ecd/geek-redis/datastruct.png)

