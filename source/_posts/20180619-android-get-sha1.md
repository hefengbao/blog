---
title: Android获取签名证书的sha1值
date: 2018-06-19 17:17:17
tags:
  - android
categories: Android
---
> 开发环境： Windows 10

一般用 `keytool` 命令获取。

打开 PowerShell：

```shell
cd  .android
```

输入如下命令，回车：

```shell
keytool
```

如果没有错误，万事大吉；如果出现 `keytool 不是内部或外部命令，也不是可运行的程序或批处理文件`, 则说明 java 环境没有配置好：

1、安装 JDK

2、新建系统变量 `JAVA_HOME`, `CLASSPATH`, 如下所示（注意自己 JDK 安装目录）
	  `JAVA_HOME D:\Java\jdk`
      ` CLASSPATH %JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar`

3、然后在 path 中分别添加：
	  `%JAVA_HOMW%\bin`
	   `%JAVA_HOME%\jre\bin`
	   
生成 sha1:

网上教程一般都用如下命令：
```shell
keytool -list -keystore debug.keystore
```

秘钥： `android`

会正确生成 sha1 ,但也会有如下提示：

```shell
Warning:
JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore debug.keystore -destkeystore debug.keystore -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。
```

按提示修改。