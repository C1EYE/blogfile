---
title: OS Persistence
tags:
  - null
category:
  - 读书笔记
index: true
info: true
date: 2021-08-11 21:49:14
---
<!-- more -->

# 第三十六章 I/O设备

> 一个程序没有任何输入（每次运行结果相同），或者没有任何输出（那运行它干嘛?）都是不合理的

## 系统架构

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815111539.png" alt="image-20210815111539170" style="zoom:50%;" />

>  自上至下越来越慢

## 标准设备

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815112221.png" alt="image-20210815112221496" style="zoom:50%;" />

一个标准设备可以分为两个部分：

- 向系统其他部件展现的硬件接口

用来让系统软件控制硬件的操作，每个设备都有自己的特定接口和对应的协议

- 内部结构

包含接口的实现，简单的设备会用几个芯片，复杂的设备会包含简单的cpu，通用内存，一些特定芯片来实现。

## 标准协议

我们将设备接口简化为三个寄存器：

- 状态寄存器 记录设备状态
- 命令寄存器 通知设备执行具体指令
- 数据寄存器 存放数据

协议可能看起来像这样：

![image-20210815113128491](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210815113128.png)

1. 轮询设备（判断是否就绪）
2. 发送数据
3. 写入指令
4. 轮询设备（判断是否完成）

这个简单的协议在轮询时浪费了大量CPU时间
