---
title: "ErrorException  : symlink(): Input/output error"
date: 2020-01-15 17:17:17
tags:
  - laravel
categories: Laravel
---
在 Windows 10 环境下， 项目中 `php artisan storage:link` 命令生成软连接，但是访问时总是提示 `404` 错误，于是远程到 homestead 环境创建软连接：

```
vagrant up

vagrant ssh

cd 项目

php artisan storage:link`
```

出现如下错误：

```
ErrorException  : symlink(): Input/output error

  at /home/vagrant/code/lsapp/vendor/laravel/framework/src/Illuminate/Filesystem/Filesystem.php:228
    224|      */
    225|     public function link($target, $link)
    226|     {
    227|         if (! windows_os()) {
  > 228|             return symlink($target, $link);
    229|         }
    230|
    231|         $mode = $this->isDirectory($target) ? 'J' : 'H';
    232|

  Exception trace:

  1   symlink("/home/vagrant/code/lsapp/storage/app/public", "/home/vagrant/code/lsapp/public/storage")
      /home/vagrant/code/lsapp/vendor/laravel/framework/src/Illuminate/Filesystem/Filesystem.php:228

  2   Illuminate\Filesystem\Filesystem::link("/home/vagrant/code/lsapp/storage/app/public", "/home/vagrant/code/lsapp/public/storage")
```

https://laracasts.com/discuss/channels/laravel/symlink-inputouput-error 这里有讨论；用 `ln` 命令创建还是会报错，

最终，解决方案参照  

https://www.cnblogs.com/youji-relog/p/11551532.html  

亲测可行。