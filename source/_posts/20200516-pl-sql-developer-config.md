---
title: PL/SQL Developer 配置
date: 2020-05-16 17:17:17
tags:
  - pl/sql
categories: PL/SQL
---
> 操作系统： Windows 10

## 安装 Instant Client

官网（[Instant Client for Microsoft Windows (x64) 64-bit | Oracle 中国](https://www.oracle.com/cn/database/technologies/instant-client/winx64-64-downloads.html)） 下载 `instantclient-basic-windows.x64-21.6.0.0.0dbru.zip` 这种即可，版本自选，下载完成后解压，添加到环境变量。

注意下载链接的 `Description` 最后一项：

>The 21c Basic package requires the [Microsoft Visual Studio 2017 Redistributable](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads).

如果没有安装，则打开 Pl/SQL 时会出现类似如下的错误：

```
不能初始化 "..\oci.dll"

Make sure you have the 64 bits Oracle Client installed.
```

按照 `Description` 中的链接下载安装。

### 语言设置：

在环境变量中加入：

```
变量名：NLS_LANG
变量值：SIMPLIFIED CHINESE_CHINA.ZHS16GBK
```

参考：

1、https://blog.csdn.net/stevendbaguo/article/details/54971914  

2、https://wenku.baidu.com/view/2297e9bc3968011ca2009130.html  



### 日期格式设置：

在环境变量中加入：

```
变量名：NLS_DATE_FORMAT
变量值：YYYY-MM-DD HH24:MI:SS

变量名：NLS_TIMESTAMP_FORMAT
变量值：YYYY-MM-DD HH24:MI:SS
```



参考：https://www.cnblogs.com/stono/p/5533492.html

环境变量->系统变量 -> 新建：

|      | 变量名               | 变量值                            |
| ---- | -------------------- | --------------------------------- |
| 时间 | NLS_DATE_FORMAT      | YYYY-MM-DD HH24:MI:SS             |
|      | NLS_TIMESTAMP_FORMAT | YYYY-MM-DD HH24:MI:SS             |
| 语言 | NLS_LANG             | SIMPLIFIED CHINESE_CHINA.ZHS16GBK |



重新启动 PL/SQL。

