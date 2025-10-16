---
title: 理解 Laravel：TrustProxies Middleware
date: 2024-10-08 17:17:17
tags:
  - laravel
  - laravel-middleware
categories: Laravel
---
Laravel 项目使用负载均衡（load balancers）或者反向代理（reverse proxies）时，可能会遇到HTTPS检测或客户端IP地址不正确的问题。Laravel 的 TrustProxies 中间件提供了解决方案。

## 理解 TrustProxies 的必要性

使用负载均衡或反向代理时，传入的请求需要经过这个中间层才能到达你的应用。这可能导致两个问题：

- HTTPS 检测可能失败，导致生成不安全的 URLs；
- 客户端的IP地址可能被错误地识别为代理的IP，而不是实际的客户端。

TrustProxies 中间件允许你的应用程序信任代理设置的标头，从而解决这些问题。

## 配置 TrustProxies

在 `bootstrap/app.php` 文件中编辑：

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->trustProxies(at: [
        '192.168.1.1',
        '10.0.0.0/8',
    ]);
})
```

此配置告诉Laravel信任IP地址为 `192.168.1.1`和 `10.0.0.0/8` 范围内的任何IP的代理。

### 信任所有代理

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->trustProxies(at: '*');
})
```
须慎用。

### 配置可信任的 Headers

```php
use Illuminate\Http\Request;

->withMiddleware(function (Middleware $middleware) {
    $middleware->trustProxies(headers: Request::HEADER_X_FORWARDED_FOR |
        Request::HEADER_X_FORWARDED_HOST |
        Request::HEADER_X_FORWARDED_PORT |
        Request::HEADER_X_FORWARDED_PROTO |
        Request::HEADER_X_FORWARDED_AWS_ELB
    );
})
```

参考：

https://www.harrisrafto.eu/securing-your-laravel-app-behind-load-balancers-mastering-the-trustproxies-middleware
