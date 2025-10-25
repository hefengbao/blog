---
title: laravel-route-fallback
date: 2025-10-24 15:53:16
tags:
  - laravel
categories: Laravel
permalink: 20251024-laravel-route-fallback
---
Laravel 的 `Route::fallback` 提供了一种优雅的方式来处理与任何定义的路由都不匹配的请求。你可以为遇到缺失页面的用户创建有意义的体验，而不是显示通用的 404 页面。

当页面被移动或重命名，或者处理旧系统的遗留 URL 时，此功能对于保持用户参与度特别有价值。它还有助于收集缺失页面的数据，为网站的结构和内容策略提供信息。

```php
Route::fallback(function () {
    return view('errors.404')
        ->with('message', 'Page not found');
});
```

你还可以使用 Request 对象来获取更多上下文：

```php
use Illuminate\Http\Request;
 
Route::fallback(function (Request $request) {
    // Access current path
    $path = $request->path();
 
    // Check if it's an API request
    if ($request->expectsJson()) {
        return response()->json(['error' => 'Not Found'], 404);
    }
 
    return view('errors.404', compact('path'));
});
```


【转】[在 Laravel 中处理不匹配的路由 | 日思录](https://tubring.cn/articles/route-fallback)