---
title: Mac 安装 ImageMagick
description: Mac 安装 ImageMagick。
typora-root-url: ..
typora-copy-images-to: ../images
date: 2020-07-20 18:38:32
categories:
tags:
- Mac
- Ruby On Rails
---

有好几个项目都用到了 `CarrierWave` ，而且会用到一些图片的处理，所以安装 `ImageMagick` 来进行一些处理。

### 安装 ImageMagick

使用 brew 安装，我这里装的是 imagemagick 6.x.x 的版本，可以选择装 imagemagick 7.x.x 的版本，另外我还装了一些别的依赖项。

```shell
# imagemagick 6.x.x 的版本
brew install imagemagick@6 jpeg libtiff jasper libpng
# imagemagick 7.x.x 的版本
brew install imagemagick jpeg libtiff jasper libpng
```

然后根据提示配置一下环境变量：

```shell
echo '
export PATH="/usr/local/opt/imagemagick@6/bin:$PATH" 
export LDFLAGS="-L/usr/local/opt/imagemagick@6/lib"
export CPPFLAGS="-I/usr/local/opt/imagemagick@6/include"
export PKG_CONFIG_PATH="/usr/local/opt/imagemagick@6/lib/pkgconfig"' >> ~/.bash_profile
```

生效刚配置的环境变量：

```shell
source ~/.bash_profile
```

最后重开一下 terminal 或者 iTerm，检查一下是否 JPEG 之类的格式是否可用就行。

```shell
identify -list format | grep JPEG
```
![终端输出](https://i.imgur.com/Oc0d7RX.png)

------

参考资料

- [问题 issue](https://github.com/carrierwaveuploader/carrierwave/issues/1486)