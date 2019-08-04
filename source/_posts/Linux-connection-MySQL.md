---
title: Linux 连接 MySQL 命令
typora-root-url: ..
typora-copy-images-to: ../images
date: 2019-03-12 15:51:15
tags: Linux
---

## <center>记录一下Linux环境下怎么连接Mysql数据库的</center><!-- more -->

连接MySQL的格式： `-h` `-u` `-p` 与**主机地址/用户名/用户密码**可以不加入空格

```shell
$ mysql -h主机地址 -u用户名 -p用户密码
```

如果是**连接远程主机**上的MySQL的话，`-h`需要**指定远程主机的IP**，其他命令则相同

进入到MySQL的话，我们会看到以下提示符：

```mysql
mysql>
```

最后操作完肯定是要退出MySQL的啦~所以我们需要输入MySQL命令：

```mysql
mysql> exit -别忘了回车
```

------

**<center>over~</center>**