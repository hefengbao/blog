---
title: Laravel 入门：05-Artisan 命令行
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
`artisan` 命令提供了许多有用的工具，可以使用 list 命令查看：

```shell
php artisan list
```

比较常用的是生成脚手架(scaffold)，可以通过运行命令 php artisan make 查看,  例如：

```shell
# 创建控制器
php artisan make:controller UserController

# 创建模型
php artisan make:model Post

# 创建请求验证文件
php artisan make:request UserRequest
```

上面的举例是最基础的，比如 php artisan make:model Post 后面还可以添加参数：

```shell
# 同时生成数据库迁移文件
php artisan make:model Post -m

# 同时生成控制器
php artisan make:model Post -c
```

可以使用 help 命令查看详细的参数说明， 例如： 

```shell
php artian help make:model
```

先了解一下，在接下来的文章中会用到大部分 `make` 相关的命令。  

参考文档 https://learnku.com/docs/laravel/9.x/artisan/12222

Demo：https://github.com/hefengbao/laravel-demo