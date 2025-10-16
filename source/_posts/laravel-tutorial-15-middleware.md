---
title: Laravel 入门：15-中间件
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
中间件提供了一种方便的机制来检查和过滤进入应用程序的 HTTP 请求。

在项目的 `app/Http/Middleware` 目录下可以看到 Laravel 预定义好的中间件，我们通过 `php artisan make:middleware` 创建的中间件也会存放到该目录下。

查看 `app/Http/Kernel.php` 文件：

```php
class Kernel extends HttpKernel
{
	// 定义全局中间件
	protected $middleware = [];
	
	protected $middlewareGroups = [
		// 应用在 routes/web.php 中定义的路由
		'web' => [],
		// 应用在 routes/api.php 中定义的路由
		'api' => []
	]
}
```

查看 `app/Providers/RouteServiceProvider.php` 文件：

```php
public function boot()
{
		$this->routes(function () {
// 这里的 api 中间间就是上面代码中 middlewareGroups 定义的 api, 下同
				Route::middleware('api')
						->prefix('api')
						->group(base_path('routes/api.php'));

				Route::middleware('web')
						->group(base_path('routes/web.php'));
		});
}
```

开头说了中间件用来检查和过滤进入应用程序的 HTTP 请求，比如我们我禁止黑名单 IP 地址访问，就可以通过中间件来实现：

```shell
php artisan make:middleware BlacklistIpDenyMiddleware
```

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class BlacklistIpDenyMiddleware
{
    public function handle(Request $request, Closure $next)
    {
        // 从数据库或缓存中获取
        $blacklistIps = [];

        if (in_array($request->ip(), $blacklistIps)){
            abort(403, 'Access Denied.');
        }
        return $next($request);
    }
}

```

这里应用在 web 路由中：

```php
 protected $middlewareGroups = [
        'web' => [
           ...
            \App\Http\Middleware\BlacklistIpDenyMiddleware::class,
        ],
}
```

还可以更细粒度的使用中间件，比如应用系统除开登录、注册等请求，其他的请求要求用户必须登录：

```php
<!-- routes/web.php -->

Route::middleware('auth')->group(function (){
    Route::resource('/users', \App\Http\Controllers\UserController::class);
});
```

还有更细粒度的角色权限控制：

```php
<!-- routes/web.php -->

Route::middleware('auth')->group(function (){
    
	Route::get('/settings',[ \App\Http\Controllers\SettingController::class, 'index] )->middleware(['role:admin']);
});
```

还可以在控制器的 `__construct` 方法中使用中间件：

```php
class HomeController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth'); // 访问该控制器中的所有方法都需要用户已登录
				
	// 或者
	$this->middleware('auth')->only(['edit','destroy]]); // 仅用于 edit、destroy 方法
				
	// 或者
	$this->middleware('auth')->except(['edit','destroy]]); // 不用于 edit、destroy 方法
    }
}
```

Demo：https://github.com/hefengbao/laravel-demo