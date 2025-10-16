---
title: 通过 Let's Encrypt 申请 HTTPS证书
date: 2021-07-13 17:17:17
tags:
  - letsencrypt
categories: HTTPS
---

[文档 - Let's Encrypt - 免费的SSL/TLS证书 (letsencrypt.org)](https://letsencrypt.org/zh-cn/docs/)

## 1、Certbot 方式：

使用 [Certbot (eff.org)](https://certbot.eff.org/)，先选择 HTTP 服务器和操作系统，我的是运行在 Ubuntu 上的 nginx:

![](images/20210713113527.png)

### 查看是否安装了 `snap`:

```shell
snap --version 
```

没有安装，则：

```shell
sudo apt install snapd
```

### 确保 `snapd` 更新到最新：

```shell
sudo snap install core;sudo snap refresh core
```

### 安装 `Certbot`:

```shell
sudo snap install --classic certbot
```

### 添加 `certbot` 到命令行：

```shell
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

查看设否设置成功：

```shell
certbot --version
```

### 生成证书

首先应配置好站点，准确的说应在 `/etc/nginx/sites-available/` 目录下配置站点信息，主要是域名信息， 并软连接到 `/etc/nginx/sites-enabled/` 目录下，并重启 `nginx`, 运行：

```shell
sudo certbot --nginx
```

仅生成证书，不修改 Nginx 配置：

```shell
sudo certbot --nginx certonly
```

手动输入域名生成证书：

```shell
sudo certbot certonly --manual
```

生成的证书有效期 90 天，开启定时任务续订：

```shell
sudo crontab -u root -e
0 3 1 * * certbot renew --dry-run
#sudo systemctl reload crond
sudo service cron reload
```

[Let's Encrypt：用免费的 SSL 证书，让网站支持 HTTPS - 宁皓网 (ninghao.net)](https://ninghao.net/blog/5592)

## 2、``acme.sh`` 方式

[How to issue a cert · acmesh-official/acme.sh Wiki (github.com)](https://github.com/acmesh-official/acme.sh/wiki/How-to-issue-a-cert)

[申请Let's Encrypt通配符HTTPS证书 - 飞奔的萝卜 - OSCHINA - 中文开源技术交流社区](https://my.oschina.net/kimver/blog/1634575)
