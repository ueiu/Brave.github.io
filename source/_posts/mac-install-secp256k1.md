---
title: Mac 安装 secp256k1
description: Mac 安装 secp256k1。
typora-root-url: ..
typora-copy-images-to: ../images
date: 2020-07-19 17:01:54
categories:
tags:
- Mac
- Ruby On Rails
---

目前有个 Rails 项目因为用了 `eth` 这个 gem，这个 gem 的话依赖 `ffi` ，然而这个东西还需要安装  `secp256k1` ，不然会出现报错，所以这里记录一下怎么安装  `secp256k1` 。

### 环境

- Mac: 10.14.6
- Ruby: 2.6.5
- Rails: 6.0.2.1

### 报错

```shell
# 报错提示
FFI::NotFoundError - Function 'secp256k1_ecdsa_sign_recoverable' not found in [libsecp256k1.dylib]
```

### 解决方案

1. 使用 `brew` 安装构建依赖项
```shell
brew install autoconf automake libtool berkeley-db4 pkg-config openssl boost boost-build libevent
```

2. 下载 `secp256k1` 
```shell
# 随便找个地方 clone 就行
git clone git@github.com:bitcoin-core/secp256k1.git
```

3. 构建 `secp256k1`
```shell
# 以下步骤都在 clone 的 secp256k1 中进行
1. sh autogen.sh
# 这一步与 github 中的不同，github 的还是会出错，视情况输入命令吧
2. sh onfigure --enable-module-recovery
3. make
4. make check
5. sudo make install
```

------

参考资料

- [secp256k1 Github](https://github.com/bitcoin-core/secp256k1)
- [构建依赖项 issue](https://github.com/meritlabs/merit/issues/344)
- [参考 stackoverflow](https://stackoverflow.com/questions/40615330/ruby-ffi-not-finding-certain-functions)