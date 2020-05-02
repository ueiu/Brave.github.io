---
title: CentOS7 系统部署 RSSHub
typora-root-url: ..
typora-copy-images-to: ../images
date: 2019-08-11 20:09:34
categories:
- Centos
tags: 
- RSSHub
- 瞎折腾
---

因为薅到了阿里云的羊毛，买入一个轻量云服务器，闲置也不好，所以拿这服务器来部署一个高效获取聚合信息的工具，就是本文的主角：**RSSHub**。

<!-- more -->

### RSSHub是什么？

> RSSHub 是一个轻量、易于扩展的 RSS 生成器，可以给任何奇奇怪怪的内容生成 RSS 订阅源。

#### 部署RSSHub需要什么？

> 部署 RSSHub 需要基本的计算机编程常识，如果您在部署过程中遇到无法解决的问题请到 [issues](https://github.com/DIYgod/RSSHub/issues) 寻找类似的问题或 [向我们提问](https://github.com/DIYgod/RSSHub/issues/new/choose)，我们会尽快给您答复
>
> 部署涉及到以下基本编程常识:
>
> 1. 命令行操作
> 2. [Git](https://git-scm.com/)
> 3. [Node.js](https://nodejs.org/)
> 4. [npm](https://www.npmjs.com/get-npm) 或 [yarn](https://yarnpkg.com/zh-Hans/docs/install)
>
> 部署到可外网访问则可能涉及到:
>
> 1. [Nginx](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)
> 2. [Docker](https://www.docker.com/get-started) 或 [docker-compose](https://docs.docker.com/compose/install/)
> 3. [Redis](https://redis.io/download)
> 4. [Heroku](https://devcenter.heroku.com/articles/getting-started-with-nodejs)
> 5. [Google App Engine](https://cloud.google.com/appengine/)

### 部署RSSHub

先下载`RSSHub`的源码：

```bash
$ git clone https://github.com/DIYgod/RSSHub.git
$ cd RSSHub
```

下载完成后，需要安装依赖

我们这里使用`npm`

有些时候时候下载依赖特别慢或者下载不成功的时候，我们可以使用`NPM镜像`

```bash
$ npm install
# 把npm源换为淘宝源
$ npm --registry https://registry.npm.taobao.org install
```

依赖下载完成后，我们在`RSSHub`目录下运行：

```bash
$ npm start
```

接着在浏览器内打开网址：http://youserveraddress:1200/ ，出现下面的RSSHub欢迎界面，就可以使用你的`RSSHub`啦！

![](https://i.loli.net/2019/08/18/uNBFr6fWLxIVba4.png)

### 始终运行RSSHub

因为是一个阅读工具，所以要让它7*24小时运行工作。

这里就需要用到[PM2](https://pm2.io/ "PM2")。`PM2`是具有负载均衡的node应用进程管理器，可以使应用程序在服务器上全天候运行，永久保持应用程序活动，无需停机即可重新加载它们。

首先我们需要全局安装`PM2`：

```bash
$ npm install pm2 -g
```

接着在`RSSHub`目录：

```bash
$ pm2 start lib/index.js --name rsshub
```

详细使用说明参照 [RSSHub指南](https://docs.rsshub.app/)，替换所有路由例子中的`https://rsshub.app/` 为 `http://youserveraddress:1200`即可正常使用。

### 后记

#### 抓取抖音生成rss失败
![](https://i.loli.net/2019/08/18/owFmgyZUKPxNve9.png)

显示的：**Error: Failed to launch chrome!** 看上去是我的Chrome没有安装，所以我们把Chrome安装好即可。

```bash
$ curl https://intoli.com/install-google-chrome.sh | bash
```

运行上面这段脚本安装命令，会自动监测当前的版本安装Chrome并自动查找并安装通常会导致安装失败的所有未满足的依赖项。

安装成功会显示：`Successfully installed Google Chrome!`

最后我们再检查一下是否还缺少依赖：

```bash
$ ldd /opt/google/chrome/chrome | grep "not found"
```

无返回的话说明CentOS7下Chrome依赖问题基本解决。

打开网址运行：http://youserveraddress:1200/douyin/user/93610979153 ，显示如下抓取就说明正常了。

![](https://i.loli.net/2019/08/18/McVfG5EUCJuayAw.png)

------

参考资料

- [RSSHub部署文档](https://docs.rsshub.app/install/ "部署 | RSSHub")
- [在CentOS上安装Google Chrome](https://intoli.com/blog/installing-google-chrome-on-centos/) 


