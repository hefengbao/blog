---
title: 垃圾评论过滤：spatie/laravel-honeypot
date: 2025-10-22 11:51:53
tags:
  - laravel
permalink: 20251022-spatie-laravel-honeypot
categories: Laravel
---
## 介绍

对于提供了评论功能的博客系统，最大的威胁或许就是垃圾评论，各种铭感词、链接，烦不胜烦。 `spatie/laravel-honeypot` 创建一个隐藏的 `div`，其中包含两个字段，即蜜罐字段和加密时间戳，用于标记页面提供给用户的时刻。当包含这些用户不可见的输入的表单提交到您的应用程序时，包附带的自定义验证器会检查蜜罐字段是否为空，并检查用户填写表单所花费的时间。如果表单填写得太快，或者蜜罐字段中输入了值，则此提交很可能来自垃圾邮件机器人。

## 安装

```shell
composer require spatie/laravel-honeypot
```

发布配置文件（可选）：

```shell
php artisan vendor:publish --provider="Spatie\Honeypot\HoneypotServiceProvider" --tag=honeypot-config
```

## 使用

```php
<form method="POST" action="...">

@honeypot

<input type="text" name="my_normal_input" value="">

...

</form>
```

在处理表单提交的路由中使用 _ProtectAgainstSpam_ 中间件。该中间件将拦截任何被困在蜜罐中的请求。如果请求的提交速度超过配置的时间，它也会拦截请求。

```php
use Spatie\Honeypot\ProtectAgainstSpam;

Route::middleware(ProtectAgainstSpam::class)->group(function() {

// Routes protected with honeypot technique

});
```

全局启用：

```php
// app/bootstrap/app.php

->withMiddleware(function (Middleware $middleware): void {  
    $middleware->append(\Spatie\Honeypot\ProtectAgainstSpam::class);  
})
```

[避免在 Laravel 9 中发送垃圾表单 - HiBit](https://www.hibit.dev/posts/63/avoid-forms-spamming-in-laravel-9)