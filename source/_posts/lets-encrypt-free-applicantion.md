---
title: Let's Encrypt 申请免费 https 证书 - 给你的网站上把小绿锁
description: 使用 acme.sh 申请 https 证书。
typora-root-url: ..
typora-copy-images-to: ../images
date: 2019-08-17 21:27:24
categories:
- 网站
tags:
- ssl 证书
- Let's Encrypt
- Nginx
---

[Let’s Encrypt](https://letsencrypt.org/) 是一家免费、开放、自动化的证书颁发机构（CA），目的是为了推进网站使用 https，所以这次我们使用它来为网站生成 https 证书。

我们使用 [acme.sh](https://acme.sh/) 来安装我们的证书，acme.sh 有中文文档的支持，更是方便。

### 安装 acme.sh
我们通过下面这个命令安装 acme.sh：

```bash
curl https://get.acme.sh | sh
```

acme.sh 会完成3个操作：

1. 把 acme.sh 安装到你的 **home** 目录下：`~/.acme.sh/`。所有证书也将放置在此文件夹中。
2. 创建一个 bash 的 alias，方便你的使用： `alias acme.sh=~/.acme.sh/acme.sh`。
3. 自动为你创建 cronjob，每天 0:00 点自动检测所有的证书，如果快过期了，需要更新，则会自动更新证书。可以通过命令 `corntab -l` 查看。

安装完成后，必须关闭当前终端重新打开使别名生效。更多用法可以参考 [WIKI 页面](https://github.com/acmesh-official/acme.sh/wiki) 或者通过命令 `acme.sh --help` 查看。

### 生成证书

acme.sh 实现了 **acme** 协议支持的所有验证协议，一般有两种方式验证：http 和 dns 验证。dns 验证又分为：手动 dns 和 dns api 方式。我们这里使用 dns api 的方式进行验证。

> dns 方式的真正强大之处在于可以使用域名解析商提供的 api 自动添加 txt 记录完成验证。
> acme.sh 目前支持 cloudflare，dnspod，cloudxns，godaddy 以及 ovh 等数十种解析商的自动集成。
>
> 更多解析商的使用可以看[这里](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)。

因为我的域名是在腾讯云的，所以我这里使用 dnspod 完成。首先我们需要 [申请 API TOKEN](https://console.dnspod.cn/account/token)：

![](https://i.imgur.com/gUsN4C9.png)

申请到 **ID** 以及 **TOKEN** 后，我们需要设置这个配置：
```bash
export DP_Id="id"
export DP_Key="token"
```

配置成功后的 `DP_Id`和 `DP_Key`将被保存`~/.acme.sh/account.conf` 中。

acme.sh 支持 RSA 证书以及 ECC 证书，所以下面有两种生成方式可供选择：

#### 生成默认 RSA 证书

```bash
acme.sh --issue --dns dns_dp -d test.com -d *.test.com
```

#### 生成 ECC 证书

```bash
acme.sh --issue --dns dns_dp -d test.com -d *.test.com --keylength ec-256
```

注意这里第一个域名为主域名，后面为泛域名。证书生成成功后，默认保存在 `~/.acme.sh/你的主域名` 中。如果生成的是 ECC 证书的话，则保存在 `~/.acme.sh/你的主域名_ecc` 中。按需选择即可。

###  copy/安装证书

证书生成以后，接下来需要把证书 copy 到真正需要用它的地方。这里仅使用 Nginx 作为示例说明，更多用法参考[这里](https://github.com/acmesh-official/acme.sh/wiki/说明#3-copy安装-证书)。

> 注意，默认生成的证书都放在安装目录下: `~/.acme.sh/你的主域名`，请不要直接使用此目录下的文件，例如：不要直接让 nginx/apache 的配置文件使用这下面的文件。这里面的文件都是内部使用，而且目录结构可能会变化。
> 正确的使用方法是使用 `--installcert` 命令，并指定目标位置，然后证书文件会被copy到相应的位置，例如：

```bash
acme.sh  --installcert  -d  test.com   \
         --key-file   /etc/nginx/ssl/test.com.key \
         --fullchain-file /etc/nginx/ssl/fullchain.cer \
         --reloadcmd  "service nginx force-reload"
```

> 一个小提醒，这里用的是 service nginx force-reload，不是 service nginx reload，据测试，reload 并不会重新加载证书，所以用的 force-reload。
> Nginx 的配置 `ssl_certificate` 使用 `/etc/nginx/ssl/fullchain.cer`，而非 `/etc/nginx/ssl/test.com.cer`，否则 [SSL Labs](https://www.ssllabs.com/ssltest/) 的测试会报 `Chain issues Incomplete` 错误。

### 更新证书

因为在安装 acme.sh 时已经创建了一个 cronjob 去定时检查更新证书，所以我们不需要操心这个事情。

### 更新 amce.sh

> 由于 acme 协议和 letsencrypt CA 都在频繁的更新，因此 acme.sh 也经常更新以保持同步。

**`Tips`** 这个东西还是建议开启自动升级的，试过忘记开启结果证书掉了哈哈哈。

升级 acme.sh 到最新版：

```bash
acme.sh --upgrade
```

如果你不想手动升级，可以开启自动升级：
```bash
acme.sh  --upgrade  --auto-upgrade
```

之后，acme.sh 就会自动保持更新了。你也可以随时关闭自动更新：
```bash
acme.sh --upgrade  --auto-upgrade  0
```

------

参考资料

- [acme.sh 使用说明](https://github.com/acmesh-official/acme.sh/wiki/说明)
- [Let's Encrypt 泛域名证书申请及配置](https://segmentfault.com/a/1190000015354547)
- [acme.sh 配合 letsencrypt 配置泛域名](https://juejin.im/post/5b6542ed51882519d3468d6d)
- [Linux 下使用 acme.sh 配置 Let's Encrypt 免费 SSL 证书 + 通配符证书](https://sb.sb/blog/linux-acme-sh-lets-encrypt-ssl/)

