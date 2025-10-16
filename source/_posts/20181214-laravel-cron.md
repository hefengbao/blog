---
title: Laravel 定时任务
date: 2018-12-14 17:17:17
tags:
  - laravel
categories: Laravel
---

> Ubuntu 16.04
> https://laravel-china.org/docs/laravel/5.7/scheduling/2287


```shell
sudo crontab -e
```

添加如下内容：

```shell
* * * * * cd /var/www/data &&  php artisan schedule:run >> /dev/null 2>&1
```

保存并退出。

重启 `cron` 服务：

```shell
service cron restart
```

查看添加的定时任务：

```shell
sudo crontab -u root -l
```