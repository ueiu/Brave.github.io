---
title: Mac 解决 Tomcat URI传参乱码问题
typora-root-url: ..
typora-copy-images-to: ../images
date: 2020-04-20 16:20:25
categories:
- Java
tags:
- Mac
- Tomcat
---

在调试本地Java Web项目的API时，应该正常显示的数据并没有出现，debug后发现API传入的参数值乱码了，所以这里记录一下我的解决办法。

### 解决方案

#### Tomcat 所在的目录

如果使用不同的安装方式根据自己的安装方找到Tomcat 所在的目录，我是采用`homebrew`安装的，所以路径如下：

```
/usr/local/Cellar/<你安装的版本>/<具体的版本号>/libexec
```

#### 编辑/conf/server.xml

找到以下这行代码，添加 `URIEncoding="utf-8"` 防止传入的参数出现中文乱码的情况。

```
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"
           URIEncoding="utf-8" />
```

最后重启一下Tomcat。

#### 修改结果

<div>
	<center>修改前参数：</center>
	<div align=center> <img src ="https://i.imgur.com/IClGs84.png"/> </div>
</div>
<br/>

<div>
	<center>修改后参数：</center>
	<div align=center> <img src ="https://i.imgur.com/clSBYz1.png"/> </div>
</div>
