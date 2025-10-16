---
title: Mysql 8 安装
date: 2019-12-03 17:17:17
tags:
  - mysql
categories: MySQL
---
# Mysql 8 安装

`Ubuntu 20.04` 可运行 `sudo apt isntall mysql-server` 命令直接安装。 

```shell
CREATE USER 'pma'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'pmapass';
GRANT SELECT, INSERT, UPDATE, DELETE ON <pma_db>.* TO 'pma'@'localhost';
```


----


## 下载：

https://dev.mysql.com/downloads/

https://dev.mysql.com/downloads/mysql/

## 文档：

https://dev.mysql.com/doc/refman/8.0/en/  

https://dev.mysql.com/doc/refman/8.0/en/binary-installation.html

https://dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/

## 安装（https://dev.mysql.com/doc/refman/8.0/en/binary-installation.html#binary-installation-layout）：

推荐安装在 `/usr/local` 目录

```shell
tar -xvf  mysql_8.tar.xz //解压 tar.xz 文件

mv mysql_8 mysql //重命名

cd mysql

mkdir data  //数据目录

groupadd mysql
useradd mysql -g mysql

chown mysql:mysql data


bin/mysqld --initialize --user=mysql --datadir /usr/local/mysql/data  // 生成 root 用户的初始密码

cp support-files/mysql.server /etc/init.d/

vi .bash_profile //编辑环境变量
		:/usr/local/mysql/bin  //在 PATH 后添加

source .bash_profile


mysql -u root -p

//修改初始密码
//5.7 set password=password('密码');
alter user user() identified by '密码';
```

https://www.cnblogs.com/keme/p/10288168.html

APT 安装：

![](images/configuring-mysql-community-server.jpg)



https://blog.csdn.net/wm609972715/article/details/83759266

[如何在Ubuntu 18.04中安装MySQL 8.0数据库服务器_数据库技术_Linux公社-Linux系统门户网站 (linuxidc.com)](https://www.linuxidc.com/Linux/2018-11/155408.htm)

Ubuntu中更改MySQL数据库文件目录:

```shell
service mysql stop

cd /media/data
cp  -a /var/lib/mysql /mysql

# 修改配置文件
vi /etc/mysql/mysql.conf.d/mysqld.cnf
# 修改 datadir
datadir         = /media/data/mysql
```

```shell
vim /etc/apparmor.d/usr.sbin.mysqld

# /var/lib/mysql r, 修改为
/media/data/mysql r,
# /var/lib/mysql/** rwk, 修改为
/data/media/mysql/** rwk,
```

```shell
service apparmor restart
service mysql restart
```





https://www.jb51.net/article/150089.htm