---
title: WAMPServer 为 Laravel 项目配置 alias
date: 2018-04-12 17:17:17
tags:
  - wamp
categories: WAMP
---
WAMPSever 可以方便的搭建 PHP 运行环境，当我们运行 Laravel 项目时（本地环境），则访问路径 是 `http://localhost/项目名称/public` ，url 有点长，这还不是主要的， 访问`http://localhost/项目名称` ，可以遍历到项目源代码，我们可以把 Apache 服务器的根目录指向项目的 public 目录，这样可以达到隐藏项目源代码的目的，可如果该服务器下有多个 Laravel 项目需要运行，这样做就不现实了， 当然，办法还是有的，可以通过配置 alias 来实现。

假若我们有 Laravel 项目 `test`, WAMPSwever 的安装目录 `D:/wamp64`,默认情况下，通过 `localhost/test/public` 访问项目。

配置 alias :
方法1、如下图：

![](images/01rV7UQCfS.png)

一步一步操作。

方法2：
在 `D:\wamp64\alias` 目录下可以看到配置好的三个 alias， 任意复制一个，修改文件名为 `test.conf`, 修改内容：

```
Alias /test "D:/wamp64/www/test/public/"

<Directory "D:/wamp64/www/test/public/">
    Options +Indexes +FollowSymLinks
    AllowOverride all
  <IfDefine APACHE24>
    Require local
  </IfDefine>
  <IfDefine !APACHE24>
     Order Deny,Allow
	  Deny from all
	  Allow from localhost ::1 127.0.0.1
	</IfDefine>
</Directory>
```

重启所有服务，访问 `localhost/test` , 可以看到 Laravel 项目的默认欢迎页。

我们在 `routes/web.php` 中添加一条路由：

```
Route::get('a',function(){
    echo 'A';
});
```

访问 `localhost/test/a` , 会出现 `404` 错误，找不到页面；
访问 `localhost/test/index.php/a` , 则可以正常访问， 可知 apache 的重写规则没有生效。

alias 译作“别名”，也可称作虚拟目录。 参考 [Apache RewriteBase 指令使用介绍](http://www.jb51.net/article/82157.htm) 理解，现在我们需要设置 `rewrite` 的基准目录：
修改 `public/.htaccess` :

```
....

# Handle Front Controller...
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    Rewritebase /test
    RewriteRule ^ index.php [L]
```

添加的内容：`Rewritebase /test` , 正是我们先前配置的 alias.

访问 `localhost/test/a`, 正常访问。

这种方法不止是对 Laravel 框架有效，对于其他如 ThingPHP 等入口文件 `index.php` 不在根目录的框架，都可如此设置。