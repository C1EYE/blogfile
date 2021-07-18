---
title: B+树
date: 2021-07-16 10:04:39
tags:
category:
---

<!-- more -->

> B+树是B树的变体，常用于数据库和操作系统的文件系统中 MySQL数据库的索引就是基于B+树实现的

-  ┌ m/2 ┐ ≤ x ≤ m

- B+树的特点 分为内部节点（非叶子）、叶子节点2种节点 **内部节点只存储key**，不存储具体数据 
- **叶子节点存储key和具体数据**

- 所有的叶子节点形成一条**有序链表**

- m阶B+树非根节点的元素数量为m

![image-20210716101918775](/Users/c1eye/Library/Application Support/typora-user-images/image-20210716101918775.png)

> 为什么要用B+树，这和磁盘IO有关

![image-20210716104304939](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210716104305.png)

![image-20210716104314655](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210716104314.png)

![image-20210716104713750](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210716104713.png)

![image-20210716104558085](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210716104558.png)

![image-20210716104358241](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210716104358.png)

- 为了减小IO操作数量，一般把一个节点的大小设计成最小读写单位的大小 
- MySQL的存储引擎InnoDB的最小读写单位是16K

- 对比B树，B+树的优势是 每个节点存储的key数量更多，树的高度更低 
- 所有的具体数据都存在叶子节点上，所以每次查询都要查到叶子节点，查询速度比较稳定 
- 所有的叶子节点构成了一个有序链表，做区间查询时更方便

![image-20210716104500773](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210716104606.png)