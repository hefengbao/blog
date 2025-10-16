---
title: Laravel 入门：07-数据库&模型
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
在框架中，一般数据库和模型是一一对应的，一个模型对应一张数据表，称之为ORM（Object Relational Mapping, 对象关系映射）。

## 一、数据库配置

一般配置修改根目录下的 .env 文件：

```shell
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel-demo
DB_USERNAME=homestead
DB_PASSWORD=secret
```

对应的配置文件是 `config/database.php` :

```php
<?php
  return [
    'connections' => [
      'mysql' => []
    ];
  ];
```

假如要连接多个数据库，在 `.env` 文件中追加配置，比如：

```shell
DB_HOST2=127.0.0.1
DB_PORT2=3306
DB_DATABASE2=laravel
DB_USERNAME2=root
DB_PASSWORD2=
```

然后在 config/database.php 文件中添加：

```php
<?php
  return [
    'connections' => [
      'mysql' => [],
      // 参考默认的配置即可
      'mysql2' => []
    ],
  ];
```

## 二、创建模型 &amp; 创建数据表（数据库迁移文件）

建议使用 `php artisan make:model XX -m` 命令行创建：

```shell
php artisan make:model Post --migration
//或者
php artisan make:model Post -m 


INFO  Model [app/Models/Post.php] created successfully.
INFO  Migration [database/migrations/2022_12_15_105154_create_posts_table.php] created successfully.
```

该命令同时创建模型和数据表，模型在 `app/ Models` 目录下， 数据表在 `database/migrations` 目录下。

编辑 `2022_12_15_105154_create_posts_table.php` （各人创建的文件名会不一样，主要是前面的时间戳） 文件：

```php
Schema::create('posts', function (Blueprint $table) {    
  $table->id();    
  $table->unsignedBigInteger('user_id')->comment('作者');    
  $table->string('title')->comment('标题');    
  $table->string('body')->comment('内容');    
  $table->timestamps();}
);
```

默认的数据库连接是 mysql（即 app/database.php 配置文件中 connections 中的 mysql）, 如果需要连接其他数据库，则可以在模型或迁移文件中指定：

```php
<!-- app/Models/Post.php -->

class Post extends Model
{ 
    protected $connection = 'mysql2';
}

//或者 database/migrations/2022_12_15_105154_create_posts_table.php

return new class extends Migration
{
    protected $connection = 'mysql2';
}

//当然，也可以同时配置
```

参考文档：

生成模型类：https://learnku.com/docs/laravel/9.x/eloquent/12251#generating-model-classes

数据库迁移：https://learnku.com/docs/laravel/9.x/migrations/12248

## 三、运行数据库迁移（在数据库中创建数据表）

```shell
php artisan migrate

php artisan migrate --force // 不要在生产环境中使用哦
```

参考文档：https://learnku.com/docs/laravel/9.x/migrations/12248#018aef

## 四、数据填充

可用 Factory 和 Seed 配合批量填充测试数据，创建 `PostFactory` 和 `PostSeeder`：

```shell
php artisan make:factory PostFactory

php artisan make:seeder PostSeeder

// 在创建模型的时候也可以同时创建 factory 和 seed
php artisan make:model Post --migration --factory --seed
//或者
php artisan make:model Post -mfs
```

参考 `database/factories/UserFactory.php` 修改 `PostFactory` :

```php
class PostFactory extends Factory
{
    public function definition()
    {
        return [
            'user_id' => 1,
            'title' => fake()->title(),
            'body' => fake()->paragraph()
        ];
    }
}
```

修改 `database/seeders/PostSeeder.php` ：

```php
class PostSeeder extends Seeder
{
    public function run()
    {
        Post::factory()->count(10)->create();
    }
}
```

运行 seeders :

```shell
php artisan db:seed --class=PostSeeder
```

可以连接数据库查询。

参考文档：https://learnku.com/docs/laravel/9.x/seeding/12249

Demo：https://github.com/hefengbao/laravel-demo