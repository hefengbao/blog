---
title: Laravel Blade：@stack
date: 2024-09-30 17:17:17
tags:
  - laravel
  - laravel-blade
categories: Laravel
---
Laravel Blade 模板中的 `@stack` 标签对于指定子视图可能需要的 JavaScript 或 CSS 文件等特别有用。

stack 顾名思义是堆栈的意思，这里的操作则是入栈。

## 基本使用

```php
// In your layout
<head>
    <!-- Head Contents -->
    @stack('scripts')
</head>
```

```php
// In a child view
@push('scripts')
    <script src="/example.js"></script>
@endpush
```

## 进阶使用

### @prepend 

加入到堆栈的开始

```php
@prepend('scripts')
    <script src="/first-to-load.js"></script>
@endprepend
```

### @pushIf

根据条件入栈

```php
@pushIf($shouldPushScript, 'scripts')
    <script src="/conditional-script.js"></script>
@endPushIf
```

## 示例

```php
// resources/views/layouts/app.blade.php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>@yield('title', 'My App')</title>
    <link rel="stylesheet" href="/app.css">
    @stack('styles')
</head>
<body>
    <nav>
        <!-- Navigation content -->
    </nav>

    <main>
        @yield('content')
    </main>

    <footer>
        <!-- Footer content -->
    </footer>

    <script src="/app.js"></script>
    @stack('scripts')
</body>
</html>
```

```php
// resources/views/posts/show.blade.php
@extends('layouts.app')

@section('title', 'View Post')

@section('content')
    <h1>{{ $post->title }}</h1>
    <p>{{ $post->content }}</p>
@endsection

@push('styles')
    <link rel="stylesheet" href="/posts.css">
@endpush

@push('scripts')
    <script src="/posts.js"></script>
@endpush
```

```php
// resources/views/posts/create.blade.php
@extends('layouts.app')

@section('title', 'Create Post')

@section('content')
    <h1>Create a New Post</h1>
    <!-- Post creation form -->
@endsection

@push('styles')
    <link rel="stylesheet" href="/markdown-editor.css">
@endpush

@push('scripts')
    <script src="/markdown-editor.js"></script>
    <script>
        initializeMarkdownEditor();
    </script>
@endpush
```

参考：

https://www.harrisrafto.eu/mastering-blade-stacks-organizing-your-laravel-views-with-precision
