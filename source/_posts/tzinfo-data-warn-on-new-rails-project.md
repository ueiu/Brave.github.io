---

title: 新建 Rails 项目 Bundle 遇到警告
description: 解决 Bundle 时遇到 tzinfo-data 警告。
typora-root-url: ..
typora-copy-images-to: ../images
date: 2020-10-05 19:27:16
categories:
- Ruby On Rails
tags:
- Ruby On Rails
- Gems
---

在下载项目依赖时，出现了 ` tzinfo-data` 这个 gem 的警告，也影响了其他依赖项的下载安装速度，所以这里记录一下怎么解决这个警告。

![](https://i.imgur.com/XaX9Z1y.png)

翻 [issue](https://github.com/tzinfo/tzinfo-data/issues/12#issuecomment-279554001) 时发现，这个 gem 是作用于 windows 上，基于 Unix 的系统之类的，基本是不需要的，当然 issue 里也提供了几种解决方案，我只采用了其中一种，感兴趣的可以去看看其它的。

> The purpose of this line is to include the tzinfo-data gem in the bundle on Windows to act as a source of time zone data. This gem is unnecessary on Ubuntu (and Unix-based systems in general) because the system includes time zone data that can be read directly by tzinfo.


### 解决方案

执行以下命令

```
bundle lock --add-platform mingw, mswin, x64_mingw, jruby
```

![](https://i.imgur.com/NHt9Ye9.png)

最后再重新执行 `bundle` 或者 `bundle install` 命令即可。

------

参考资料

- [tzinfo-data issue](https://github.com/tzinfo/tzinfo-data/issues/12#issuecomment-279554001)