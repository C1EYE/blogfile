---
title: MySQL优化
date: 2021-07-14 23:01:57
tags:
category:
---

<!-- more -->

> SQL高级内容

# 两种安装方式

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

