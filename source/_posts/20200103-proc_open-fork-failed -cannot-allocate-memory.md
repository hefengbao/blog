---
title: "proc_open(): fork failed - Cannot allocate memory"
date: 2020-01-03 17:17:17
tags:
  - wsl
  - ubuntu
categories: Linux
---
执行 `composer update` 出现如下错误：

```
Updates: symfony/service-contracts:v2.0.1, symfony/mime:v5.0.2, symfony/css-selector:v5.0.2, symfony/translation-contracts:v2.0.1
  - Updating symfony/service-contracts (v1.1.8 => v2.0.1): The following exception is caused by a lack of memory or swap, or not having swap configured
Check https://getcomposer.org/doc/articles/troubleshooting.md#proc-open-fork-failed-errors for details

In Process.php line 344:

  [ErrorException]                                   
  proc_open(): fork failed - Cannot allocate memory
```

这一般是出现在低内存的虚拟主机上，解决如下，配置交换内存：

```shell
/bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
/sbin/mkswap /var/swap.1
/sbin/swapon /var/swap.1
```

https://getcomposer.org/doc/articles/troubleshooting.md#proc-open-fork-failed-errors

https://segmentfault.com/a/1190000012533758