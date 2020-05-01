---
title: 切换 NPM 镜像源
description: 设置 NPM 镜像为淘宝镜像。
typora-root-url: ..
typora-copy-images-to: ../images
date: 2020-05-01 15:43:12
categories:
tags:
- NPM
---

### 使用淘宝镜像

#### 临时使用下载模块

```bash
npm --registry https://registry.npm.taobao.org install hexo
```

#### 默认使用淘宝镜像

```bash
npm config set registry https://registry.npm.taobao.org
```

#### 使用 CNPM

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

### 使用官方镜像

```bash
npm config set registry https://registry.npmjs.org
```

### 查看 NPM 镜像源地址

```bash
npm config get registry
```

