---
title: Docker中导入/导出PostgreSql
typora-root-url: ..
typora-copy-images-to: ../images
date: 2019-08-04 18:09:00
categories:
- Docker
tags:
---

​	因为有个项目PostgreSql数据库是运行在Docker容器中的，现在想把里面的数据导出来迁移导入到另一个机器上，一起看看怎么操作吧。

<!-- more -->

### 什么是Docker Volume？
在操作之前，我们要先了解一下Docker Volume ，这样才方便理解我们之后的操作。
Docker Volume：翻译为数据卷，用于持久化数据以及容器之间共享数据，他挂载于宿主机上。所以即使删掉了某个容器，数据依然会存在我们宿主机中，保证数据的安全性。
#### 查看Docker容器的Dcoker Volume信息
我们使用docker inspect命令来查看Docker容器的详细信息：

```bash
# inspect 后面可以输入容器ID或者是容器名
➜ ~ docker inspect 101768619d0c
"Mounts": [
  {
    "Type": "bind",
    "Source": "/project/server/db/data/pg",
    "Destination": "/var/lib/postgresql/data",
    "Mode": "rw",
    "RW": true,
    "Propagation": "rprivate"
  }
]
```

输入之后在命令行会看到很多的信息，我们找到上面代码块的`Mounts`就好了，这里面就有我们所需要的：

- `Source`表示在宿主机上的目录，可以看到是`/project/server/db/data/pg`
- `Destination`表示在容器中的目录，可以看到是`/var/lib/postgresql/data`

### 进入Docker PostgreSql容器

查询到了挂载目录之后，我们要做的是进入PostgreSql容器：

```bash
➜ ~ docker exec -it 101768619d0c bash
root@101768619d0c:/#
```

### 导出容器中的数据库

```bash
# dev 是要到导出的数据库名，根据自己需要更换
root@101768619d0c:/# pg_dump -U ueiu dev > /var/lib/postgresql/data/dev.sql    
```

等待一会我们就可以在上面提到的`Source`目录中看到`dev.sql`这个文件啦。

```bash
# 这是在容器内查询
root@101768619d0c:/# cd var/lib/postgresql/data/
root@101768619d0c:/var/lib/postgresql/data# ls
dev.sql

# 这是在宿主机目录查询
➜  server cd db/data/pg
➜  pg ls
dev.sql
```

### 导入数据到容器中的数据库

```bash
# dev 是要到导入的数据库名，根据自己需要更换
root@101768619d0c:/# psql -U ueiu dev < /var/lib/postgresql/data/dev.sql
```

虽然可以在`Source`目录中看到`dev.sql`这个文件，但导入时的路径还是要填`Destination`的路径，不然会提示找不到路径/文件的。