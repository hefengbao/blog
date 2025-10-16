---
title: windows10 搭建 php 环境
date: 2025-06-09 17:17:17
tags:
  - php
categories: PHP
---
Windows 10 操作系统

### 下载

https://windows.php.net/download#php-7.3

下载 Non Thread Safe 版本

### 安装：

比如解压到  `D:/php`, 在环境变量 `Path` 中添加 `D:/php`

进入安装目录，复制 `php.ini-development`文件， 修改为 `php.ini`

在 `php.ini` 中修改：

![](images/20200206161608.png)

新打开 `cmd` 或 `Windows PowerShell` 等， 输入 `php -v` 测试是否成功

### 开启 Oracel  Oci

下载 instant-client：

https://www.oracle.com/cn/database/technologies/instant-client/downloads.html

解压到 `D:/OracleClient`, 在环境变量 `Path` 中添加 `D:/OracleClient`, 在环境变量中添加：

![](images/20200206160233.png)

在 `php.ini` 中去掉 `extension=oci8_12c` 前面的分号，

新打开 `cmd` 或 `Windows PowerShell` 等， 输入 `php -m` 测试是否成功



