---
title: Laravel：系统登录后的跳转
date: 2017-11-09 17:17:17
tags:
  - laravel
categories: Laravel
---
Laravel 自带的 Auth,在登陆或注册成功后，会跳转到 `/home`, 但是我们的项目有时会划分前、后台，而只有后台需要登陆，则需要修改跳转路径到后台根目录，在`Controllers\Auth` 目录下，修改 `LoginController.php`、`RegisterController.php`,`ResetPasswordController.php` 中的 ` $redirectTo` 参数：

```shell
protected $redirectTo = '/admin';
```

但还是没有完成，比如我们在浏览器中打开系统后台，会跳转到登录界面，输入账号密码登陆，成功后，我们不退出系统，不关闭浏览器，只关闭当前标签页（实际操作中就可能出现这种情况），还没做事呢！于是重新打开系统，这次不会出现登陆界面，因为我们已登录了， `session` 还没过期，但是也没有正常进入后台，而是跳转到了 `/home`,所以我们还需做一点修改，在 `Middleware` 目录下，有 `RedirectIfAuthenticated.php` 这个文件，根据名称可以看出，是用来控制系统已经登陆后的跳转，修改文件中的跳转路径：

```shell
if (Auth::guard($guard)->check()) {
    return redirect('/admin');
}
```