---
title: Laravel 入门：03-概览
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
在 IDE 中打开项目文件，可以参考文档中的『目录结构』（https://learnku.com/docs/laravel/9.x/structure/12202） 先了解一些各个目录、文件的功用。

## 一、统一入口

在学习 PHP 的时候，我们知道访问站点的时候默认会访问根目录下的 `index.php` 文件，而 Laravel 的入口文件是 `public/index.php`  ，也是唯一的一个 index.php，称为统一入口，所有的访问都是经该入口转发到路由文件，比如： `http://laravel-demo.test/index.php/articles`，通常部署站点的时候都会在  url 中隐去 `index.php`，于是访问的 url 变成 `http://laravel-demo.test/articles` ，这个操作是在 Web 服务器配置的（先了解一下就好）：

`Apache` 配置参考 `public/.htaccess` ：

```
# Send Requests To Front Controller...
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^ index.php [L]
```

Nginx 配置参考 https://learnku.com/docs/laravel/9.x/deployment/12204#62e0b5 :

```
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

上面提到的路由，具体是在 routes 目录下定义，初学关注 `web.php` 即可。

## 二、 MVC

Laravel是一款采用了mvc架构模式的 Web 开发框架：

- M 是模型（model）,定义在 app/Models 目录下；
- V 是视图（view），定义在 resources/views 目录下；
- C 是控制器（controller），定义在 app/Http/Controllers 目录下。

通常定义的每条路由对应一个控制器中的一个方法，在该方法中，可以根据一个或多个模型获取数据，然后渲染到一个视图文件。

### 2.1 Controller

主要的业务逻辑都在这里，知识点比较多：

请求：

响应：

表单验证：

缓存：

文件存储：

集合：

事件：

邮件：

等等。

### 2.2 Model

一个模型往往对应一张数据表，模型是和数据库交互的桥梁，知识点主要是：数据库（https://learnku.com/docs/laravel/9.x/database/）和 Eloquent ORM （https://learnku.com/docs/laravel/9.x/eloquent）

### 2.3 View

主要知识点是Blade模板（https://learnku.com/docs/laravel/9.x/blade/12216）和视图（https://learnku.com/docs/laravel/9.x/views/12215）

Demo：https://github.com/hefengbao/laravel-demo