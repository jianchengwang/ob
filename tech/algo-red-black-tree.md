---
title: algo-red-black-tree
createdAt: 2021-02-12
categories: 
- java
- algo
tags: 
- algo
draft: true

---

[红黑树](https://zh.wikipedia.org/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)，英语：Red–black tree）是一种[自平衡二叉查找树](https://zh.wikipedia.org/wiki/自平衡二叉查找树)，是在[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)中用到的一种[数据结构](https://zh.wikipedia.org/wiki/数据结构)，典型用途是实现[关联数组](https://zh.wikipedia.org/wiki/关联数组)。它在1972年由[鲁道夫·贝尔](https://zh.wikipedia.org/wiki/鲁道夫·贝尔)发明，被称为"**对称二叉B树**"，它现代的名字源于Leo J. Guibas和[Robert Sedgewick](https://zh.wikipedia.org/wiki/Robert_Sedgewick)于[1978年](https://zh.wikipedia.org/wiki/1978年)写的一篇论文。红黑树的结构复杂，但它的操作有着良好的最坏情况[运行时间](https://zh.wikipedia.org/wiki/算法分析)，并且在实践中高效：它可以在[{\displaystyle {\text{O}}(\log n)}![{\displaystyle {\text{O}}(\log n)}](https://wikimedia.org/api/rest_v1/media/math/render/svg/67697a0b44080bbf967c00d60bf4aac79f9ce385)](https://zh.wikipedia.org/wiki/大O符号)时间内完成查找、插入和删除，这里的{\displaystyle n}![n](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b)是树中元素的数目。

这几天瞄了下jdk1.8的hashmap，当链表长度大于8的时候，哈希表数量大于64的时候，会将链表转成红黑树，这里简单做个记录。

<!--more-->







## 相关链接

[我画了 20 张图，给女朋友讲清楚红黑树](https://www.cxyxiaowu.com/7374.html)

