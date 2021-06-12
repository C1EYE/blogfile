---
title: 搭建MC服务器
date: 2021-06-06 19:38:03
tags: 
- MC
category: 服务器
---

之前租了个阿里云服务器，本来是挂着打卡，后来打卡脚本崩了就闲着。正好最近想玩会儿MC就搭建了个服务器

## 环境

- Node

```shell
sudo yum install epel-release
sudo yum install nodejs
node --version
```

> 别的方法各种找不到命令，懒得研究那些配置文件

- Java

## 管理界面

- MCSM

```shell
git clone https://gitee.com/Suwingser/MCSManager.git
# 克隆仓库(若没有 git，请安装它 yum install git)
git clone https://gitee.com/Suwingser/MCSManager.git
# 进入目录
cd MCSManager/
# 安装依赖库
npm install
# 启动面板
npm start 
```

## 选择一个服务器版本

有好几个主流服务器可选

目前用的是page

## 注意

- 调一下服务器配置有效避免卡顿