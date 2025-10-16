---
title: Laravel API 速率限制（Rate Limiting）
date: 2024-09-06 17:17:17
tags:
  - laravel
  - laravel-api
categories: Laravel
---
Laravel 提供了强大的内置工具来实现速率限制，防止 API 滥用,本文探讨如何有效地利用这些功能。

## 基础使用

Laravel 的速率限制通常使用中间件来应用。例如：

```php
Route::middleware(['auth:api', 'throttle:60,1'])->group(function () {
    Route::get('/user', function () {
        return auth()->user();
    });
});
```

`/user` 端点每分钟可被访问 60 次。

## 自定义速率限制器

在一些复杂的场景，需要自定义速率限制器，在 `AppServiceProvider ` 中定义或者使用特定的服务提供者（Service Provider）。下面示例在 `AppServiceProvider` 的 `boot` 方法中定义：

```php
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Support\Facades\RateLimiter;

public function boot()
{
    RateLimiter::for('api', function (Request $request) {
        return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
    });
}
```

应用：

```php
Route::middleware(['throttle:api'])->group(function () {
    // API routes
});
```

## 动态速率限制

例如你可以更具用户角色或者订阅级别设置速率：

```php
RateLimiter::for('premium', function (Request $request) {
    return $request->user()->isPremium() 
        ? Limit::perMinute(100) 
        : Limit::perMinute(30);
});

Route::middleware(['auth:api', 'throttle:premium'])->group(function () {
    Route::post('/process', [DataController::class, 'process']);
});
```

高级用户比普通用户有更多的调用次数。


## 全局限制 vs 特定路由限制

在 `app/Http/Kernel.php` 中设置全局限制：

```php
protected $middlewareGroups = [
    'api' => [
        'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
];
```

或者对特定路由限制：

```php
Route::post('/login', [AuthController::class, 'login'])
    ->middleware('throttle:5,1'); // 5 attempts per minute
```

## 处理速率限制异常

当调用次数超过限制频率， Laravel 抛出 `Illuminate\Http\Exceptions\ThrottleRequestsException` 异常。Laravel 11 之前的版本在 `app/Exceptions/Handler.php` 捕获：

```php
public function render($request, Throwable $exception)
{
    if ($exception instanceof ThrottleRequestsException) {
        return response()->json([
            'error' => 'Too many requests. Please try again later.'
        ], 429);
    }

    return parent::render($request, $exception);
}
```

进阶用法：

## 滑动窗口速率限制（Sliding Window Rate Limiting）

想要更加精细的控制，可以使用 Sliding Window Rate Limiting：

```php
RateLimiter::for('sliding', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});
```

## 使用 Redis 实现速率限制（Rate Limiting with Redis）

对于高流量的应用，使用 Redis 实现速率限制。

```php
RateLimiter::for('redis', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip())->response(function () {
        return response('Custom rate limit exceeded message', 429);
    });
});
```

## 组合使用

```php
Route::middleware(['throttle:global,throttle:api'])->group(function () {
    // Routes that need to satisfy both global and API-specific limits
});
```

参考：

https://www.harrisrafto.eu/securing-your-laravel-apis-with-built-in-rate-limiting/