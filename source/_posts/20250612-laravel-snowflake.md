---
title: Laravel Snowflake
date: 2025-06-12 17:17:17
tags:
  - laravel
categories: Laravel
---
用来生成类似于 Twitter 雪花算法的 64 位识别码。

## 安装

```shell
composer require "kra8/laravel-snowflake"

php artisan vendor:publish --provider="Kra8\Snowflake\Providers\LaravelServiceProvider"
```

## 用例

获取实例

```php
$snowflake = $this->app->make('Kra8\Snowflake\Snowflake');
```

或者

```php
$snowflake = app('Kra8\Snowflake\Snowflake');
```

 生成雪花算法标识码

```php
$id = $snowflake->next();
```

## 在 Eloquent 中使用

将 `Kra8\Snowflake\HasSnowflakePrimary` trait 添加到 Eloquent 模型。该 trait 实现了将数据库主键设置为雪花算法生成的ID类型，并自动将 `$incrementing` 属性设置为 `false`。

```php
<?php
namespace App;

use Kra8\Snowflake\HasSnowflakePrimary;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasSnowflakePrimary, Notifiable;
}
```

Column type `id` is supported.

```php
public function up(): void
{
    Schema::create('users', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email')->unique();
        $table->string('password');
        $table->rememberToken();
        $table->timestamps();
    });
}
```

## JavaScript 支持

由于JavaScript无法处理64位整数，因此还提供了 `HasShortPrimary` trait，它会生成一个可由JavaScript 处理的 53 位整数 ID。

使用方法：只需将 `HasSnowflakePrimary` 替换为 `HasShortPrimary` 即可。

```php
<?php
namespace App;

use Kra8\Snowflake\HasShortflakePrimary;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasShortflakePrimary, Notifiable;
}
```

技术说明：

> JavaScript 的 Number 类型仅支持53位整数精度（IEEE 754双精度限制），而雪花算法生成的ID通常是64位。HasShortPrimary 通过压缩时间戳/工作节点等位域，生成兼容JavaScript的53位ID变体。



参考：

[kra8/laravel-snowflake: This Laravel package to generate 64 bit identifier like the snowflake within Twitter.](https://github.com/kra8/laravel-snowflake)