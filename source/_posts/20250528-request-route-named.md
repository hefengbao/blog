---
title: Laravel 中的智能路由检测
date: 2025-05-28 17:17:17
tags:
  - laravel
  - laravel-route
categories: Laravel
---
Laravel 的 `named` 方法提供了一种干净的方法来确定当前请求是否与特定路由名称匹配。这个强大的功能允许你根据当前路由执行条件逻辑，非常适合分析、导航突出显示或权限检查。

当构建需要根据当前路由表现不同的组件时，这种方法变得特别有价值，而无需在整个应用中编写重复的条件检查。

```php
if ($request->route()->named('dashboard')) {
    // We're on the dashboard
}
```

以下是实现动态导航状态的实例：

```php
<?php
 
namespace App\View\Components;
 
use Illuminate\View\Component;
use Illuminate\Http\Request;
 
class NavigationMenu extends Component
{
    public function __construct(private Request $request)
    {
    }
 
    public function isActive(string $routeName): bool
    {
        return $this->request->route()->named($routeName);
    }
 
    public function isActiveSection(string $section): bool
    {
        return $this->request->route()->named("$section.*");
    }
 
    public function render()
    {
        return view('components.navigation-menu', [
            'sections' => [
                'dashboard' => [
                    'label' => 'Dashboard',
                    'route' => 'dashboard',
                    'active' => $this->isActive('dashboard')
                ],
                'posts' => [
                    'label' => 'Blog Posts',
                    'route' => 'posts.index',
                    'active' => $this->isActiveSection('posts')
                ],
                'settings' => [
                    'label' => 'Settings',
                    'route' => 'settings.index',
                    'active' => $this->isActiveSection('settings')
                ]
            ]
        ]);
    }
}
```

在应用中使用时，该导航组件自动检测当前路由并进行相应的更新：

```plaintext
<!-- navigation-menu.blade.php -->
<nav>
    @foreach($sections as $key => $section)
        <a href="{{ route($section['route']) }}"
           @class(['nav-link', 'active' => $section['active']])>
            {{ $section['label'] }}
        </a>
    @endforeach
</nav>
```

`named` 方法简化了基于路由的逻辑，使得代码更具可维护性，并减少路由依赖的复杂性。

[Laravel 中的智能路由检测](https://www.tubring.cn/articles/request-route-named)