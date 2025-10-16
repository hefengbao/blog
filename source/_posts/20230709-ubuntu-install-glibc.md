---
title: Ubuntu 安装 glibc
date: 2023-07-09 17:17:17
tags:
  - glibc
categories: Ubuntu
---
下载地址：http://ftp.gnu.org/gnu/glibc/

```shell
cd ~

wget http://ftp.gnu.org/gnu/glibc/glibc-2.37.tar.gz

tar -zxvf glibc-2.37.tar.gz

cd glibc-2.37

mkdir build

cd build 

 ../configure --prefix=/opt/glibc-2.37
```

安装过程中遇到如下错误：

```shell
configure: error:
*** These critical programs are missing or too old: bison
*** Check the INSTALL file for required versions.
```

”缺啥补啥“😀：

```shell
 sudo apt install bison
```

继续运行：

```shell
 ../configure --prefix=/opt/glibc-2.37
 ```
 
 最后：

```shell
sudo make && sudo make install
```

参考：

https://sourceware.org/glibc/wiki/Testing/Builds

https://blog.csdn.net/mengzhongsuiyi521/article/details/88432237