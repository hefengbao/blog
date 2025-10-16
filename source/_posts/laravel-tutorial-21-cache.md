---
title: Laravel 入门：21-缓存
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
在项目中，对于一些查询比较频繁的数据可以使用缓存，以加速响应，减少数据库查询。本篇文章说说 `redis` 作为驱动的缓存系统。清除缓存，Clear Cache, `body` = 在项目中，对于一些查询比较频繁的数据可以使用缓存，以加速响应，减少数据库查询。本篇文章说说 `redis` 作为驱动的缓存系统。

## Redis 作为缓存驱动

首先安装 `predis/predis`：

```shell
composer require predis/predis
```

使用示例：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Redis;

class CacheController extends Controller
{
    public function index()
    {
        /*
         * 参数 key: 缓存标识
         * 参数 value: 缓存值，
         * 参数 ttl: 缓存时间，单位为秒，默认为null,表示无限期存储
         */
        Cache::put('active_user_count', 10, 24 * 60 * 60);

        /*
         * 和 Cache::put() 等同
         */
        \cache('active_user_count', 10, 24 * 60 * 60);

        /*
         *参数 key: 缓存标识
         * 参数 default: 指定默认值
         */
        Cache::get('active_user_count', 0);

        \cache('active_user_count', 0);

        /**
         * 如果 active_user_count 缓存不存在，则返回闭包中的结果
         */
        Cache::get('active_user_count', function (){
            return \Illuminate\Support\Facades\DB::select('select count(*) from uses where active = 1');
        });
    }

    public function redis()
    {
        /*
         * 用户 1 今日签到
         */
        Redis::sAdd('sign:2023:user:1', date('Y-m-d'));

        /*
         * 用户 1 今日是否已签到签到
         */
        Redis::sIsMemeber('sign:2023:user:1', date('Y-m-d'));

        /*
         * 用户 1 2023 年签到天数
         */
        Redis::sCard('sign:2023:user:1');
    }
}
```

需要学习 Redis 相关知识。

## 清除缓存

### 使用 Artisan 命令行工具

```shell
# 清除所有缓存
php artisan cache:clear

# 清除路由缓存
php artisan route:clear

# 清除配置缓存
php artisan config:clear

# 清除编译的视图缓存
php artisan view:clear
```

### 通过浏览器删除缓存

```php
Route::get('/clear-cache', function() {
    $exitCode = Artisan::call('cache:clear');
    return 'Application cache has been cleared';
});
```

访问示例：http://127.0.0.1:8000/clear-cache

Demo：https://github.com/hefengbao/laravel-demo