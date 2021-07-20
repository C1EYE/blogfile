---
title: MySQL优化
date: 2021-07-14 23:01:57
tags:
category:
---

<!-- more -->

> SQL高级内容

# 安装和配置

- RPM安装

```shell
rpm -ivh 文件路径
```

- 查询是否安装

```shell
rpm -qa|grep -i mysql
```

- 开启mysql服务

```shell
service mysql start
```

- 关闭

```sh
service mysql stop
```

- 设置密码

```sh
/usr/bin/mysqladmin -u root password 密码
```

- 自启动

```sh
chkconfig mysql on
//或者
ntsysv
```

- 查找路径

```sh
ps -ef|grep mysql
```

## 目录结构

![image-20210718112006860](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210718112006.png)

## MySQL架构

![image-20210718121002919](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210718121002.png)

1. 连接层
2. 服务层
3. 引擎层
4. 存储层

## 查看当前引擎

```mysql
 show variables like "%storage_engine%";
```

![image-20210718184432933](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210718184432.png)

![](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210718184449.png)

# SQL优化

## SQL执行顺序

- 手写

略

- 机读

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210718185820.png" alt="image-20210718185820809" style="zoom:50%;" />

![image-20210718191324521](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210718191324.png)

## SQLjoin

略

## 索引

> 什么是索引

- 排好序的快速查找数据结构

**分类**

- 单值索引 只包含一个列
- 唯一索引 索引值必须唯一，允许空
- 复合索引 包含多个列

![image-20210718212434472](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210718212434.png)

> UNIQUE可以为空，除了NULL之外不允许重复

**索引结构**

- BTree（重点）
- Hash
- full-text
- R-Tree

