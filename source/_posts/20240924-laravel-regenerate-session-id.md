---
title: Laravel 重设 Session ID
date: 2024-09-24 17:17:17
tags:
  - laravel
  - laravel-session
categories: Laravel
---
在 Web 应用中，Session ID 用户维护浏览器（客户端）和服务器端的连接状态，用户登录时选择“记住我”，下次打开时默认是登录状态。出于一些安全因素的考虑，可以通过重新生成 Session ID 而断开连接：比如用户修改密码后要求重新登陆，一个账号在新的浏览器登陆后强制其他浏览器的登陆状态下线。

手动生成 Session ID

```php
$request->session()->regenerate();
```

销毁 Session (ID)

```php
$request->session()->invalidate();
```

案例：

用户修改密码后重新生成 Session ID:

```php
public function changePassword(Request $request)
{
    $request->validate([
        'current_password' => ['required', 'current_password'],
        'new_password' => ['required', 'confirmed', 'min:8'],
    ]);

    $user = $request->user();
    $user->update([
        'password' => Hash::make($request->new_password),
    ]);

    // Regenerate the session ID
    $request->session()->regenerate();

    return redirect()->route('profile')
        ->with('status', 'Password changed successfully.');
}
```

使用 remember token 认证后，重新生成 Session ID：

```php
if (Auth::viaRemember()) {
    $request->session()->regenerate();
}
```

参考：

https://www.harrisrafto.eu/enhancing-security-with-session-id-regeneration-in-laravel/
