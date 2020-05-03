---
title: 配置 Docker 镜像加速
description: 配置镜像加速，拉取镜像不再困难。
typora-root-url: ..
typora-copy-images-to: ../images
date: 2020-05-03 17:47:41
categories:
- Docker
tags:
- Mac
- Centos
---

现在越来越多的开源项目以及公司项目用上了 Docker，但是在国内拉取镜像经常会遇到无法拉取或者拉取缓慢导致失败的情况。在这个时候我们可以配置 Docker 的镜像加速，Docker 官方以及国内的云服务商大多都提供了相应的加速服务。

### Docker 镜像加速服务

1. [阿里云](https://cr.console.aliyun.com/)：登录后可在[这里](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)获取到加速器地址

   ```
   https://your_code.mirror.aliyuncs.com
   ```

2. [七牛云](https://kirk-enterprise.github.io/hub-docs/#/user-guide/mirror)：支持三种镜像来源的镜像加速，包括Docker Hub、GCR、Quay。

   ```
   https://reg-mirror.qiniu.com
   https://gcr-mirror.qiniu.com
   https://quay-mirror.qiniu.com
   ```

3. [网易云](https://c.163yun.com/hub)：

   ```
   https://hub-mirror.c.163.com
   ```

4. [腾讯云](https://cloud.tencent.com/document/product/457/9113)：

   ```
   https://mirror.ccs.tencentyun.com
   ```

5. [中科大镜像站](https://mirrors.ustc.edu.cn/help/dockerhub.html)：支持三种镜像来源的镜像加速，包括Docker Hub、GCR、Quay。

   ```
   https://docker.mirrors.ustc.edu.cn
   https://gcr.mirrors.ustc.edu.cn
   https://quay.mirrors.ustc.edu.cn
   ```

6. [Azure 中国镜像](https://github.com/Azure/container-service-for-azure-china/blob/master/aks/README.md#22-container-registry-proxy)：支持三种镜像来源的镜像加速，包括Docker Hub、[GCR](https://github.com/ustclug/mirrorrequest/issues/91)、[Quay](https://github.com/ustclug/mirrorrequest/issues/135)。

   ```
   https://dockerhub.azk8s.cn
   https://gcr.azk8s.cn
   https://quay.azk8s.cn
   ```

7. [DaoCloud 镜像站](https://daocloud.io/mirror)：

   ```
   http://f1361db2.m.daocloud.io
   ```

8. [Docker 中国官方镜像](https://docker-cn.com/)：

   ```
   https://registry.docker-cn.com
   ```


### 配置加速地址

1. Centos

   创建或者修改daemon配置文件 `/etc/docker/daemon.json` 使用加速服务。

   ```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
    "https://your_code.mirror.aliyuncs.com",
    "https://reg-mirror.qiniu.com",
    "https://gcr-mirror.qiniu.com",
    "https://quay-mirror.qiniu.com",
    "https://hub-mirror.c.163.com",
    "https://mirror.ccs.tencentyun.com",
    "https://docker.mirrors.ustc.edu.cn",
    "https://gcr.mirrors.ustc.edu.cn",
    "https://quay.mirrors.ustc.edu.cn",
    "https://dockerhub.azk8s.cn",
    "https://gcr.azk8s.cn",
    "https://quay.azk8s.cn",
    "http://f1361db2.m.daocloud.io",
    "https://registry.docker-cn.com"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
   ```

2. Mac
  打开 Docker -> Perferences -> Dameon -> Advanced 编辑 json 文件。将 Centos 中示例的加速地址放入 `registry-mirrors` 数组中，点击 Apply & Restart 按钮，等待 Docker 重启并应用配置的镜像加速服务。

  ![编辑 Docker Dameon.json 文件](https://i.imgur.com/ocOgNcE.png)

### 验证配置

命令行执行命令：`docker info`，如果看到以下内容，说明配置成功。

```
Registry Mirrors:
 .....
 https://registry.docker-cn.com/
```

------

参考资料

- [Docker Hub 镜像加速器](https://juejin.im/post/5cd2cf01f265da0374189441)