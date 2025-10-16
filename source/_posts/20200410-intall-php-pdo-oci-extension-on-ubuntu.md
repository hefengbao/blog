---
title: " Ubuntu 安装 PHP 的 PDO_OCI 扩展"
date: 2020-04-10 17:17:17
tags:
  - oci
categories: PDO
---
已经用 `apt` 安装了 `php7.3-cli`,`php7.3-fpm`,`php7.3-dev` 等。
安装了 Oracle 的 `Instant Client`（可参考：https://hefengbao.github.io/helloword/ubuntu-install-oracle-instant-client-and-oci-20180511/ ），现在安装 `pdo_oci`, 貌似只能通过源码安装：

### 下载 php 源码
wget https://www.php.net/distributions/php-7.3.16.tar.xz

```
tar -xvf php-7.3.16.tar.xz

cd php-7.3.16/ext/pdo_oci/

```

`phpize` 

```
 /usr/bin/phpize7.3
```
结果如下：
Configuring for:
PHP Api Version:         20180731
Zend Module Api No:      20180731
Zend Extension Api No:   320180731

```
./configure --with-pdo-oci=instantclient,/usr/lib/oracle/19.5/client64/lib --with-oci8=instantclient,/usr/lib/oracle/19.5/client64/lib -with-php-config=/usr/bin/php-config7.3
```
```
sudo make

sudo make install
```
结果如下：
Installing shared extensions:     /usr/lib/php/20180731/

把 `extension=pdo_oci` 添加到 `/etc/php/7.3/cli/php.ini`、`/etc/php/7.3/fpm/php.ini`

`php -m` 查看。

参考：

https://www.fujieace.com/php/php-extensions/config-m4.html
https://blog.csdn.net/Zhang_Ying_Jie/article/details/79315022
https://blogs.oracle.com/opal/updated-php-72-pdo_oci-install-configure-syntax