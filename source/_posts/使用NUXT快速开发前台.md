---
title: Nuxt快速开发前台
date: 2021-05-24 10:14:33
tags: Nuxt
category: 前端
---
## Nuxt

Nuxt.js 是一个基于 Vue.js 的通用应用框架。

通过对客户端/服务端基础架构的抽象组织，Nuxt.js 主要关注的是应用的 **UI 渲染**。

- 一个服务端渲染框架

## 快速搭建环境

1. 去把脚手架文件下载下来
2. 放到一个文件夹里
3. npm install
4. npm run dev

## 使用模板快速开发

- assets放静态资源
- pages放页面
- 注意，每个页面都叫index.vue，文件夹就是路径
- layouts放布局文件

Nuxt 页面由三部分组成，head,font和中间内容，其中head和font在layout中写，内容在page中写，路由的时候使用iframe把内容替换掉（不用你做）



