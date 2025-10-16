---
title: Laravel 入门：16-事件系统
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
Laravel 的事件系统基于观察者模式，由事件（Event）和监听者（Listener），事件类保存在 `app/Events` 目录下，监听者类存储在 `app/Listeners` 目录下， 我们可通过 `php artisan make:event` 和 `php artisan make:listener` 创建。

比如博客项目中文章被评论就可以产生一个事件 `PostCommented` , 监听到 `PostCommented` 事件，就可在监听者类`PostCommentedNotification` 中做一些操作，例如给文章作者发通知邮件。

创建事件类 `PostCommented` ：

```shell
php artisan make:event PostCommented
```

```php
<?php

namespace App\Events;

class PostCommented
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct()
    {
        //
    }
		
    public function broadcastOn()
    {
        return new PrivateChannel('channel-name');
    }
}

```

创建监听者类 `PostCommentedNotification` ：

```shell
php artisan make:listener PostCommentedNotification --event=PostCommented
```

```php
<?php

namespace App\Listeners;

class PostCommentedNotification
{
    public function __construct()
    {
        //
    }

    public function handle(PostCommented $event)
    {
        //
    }
}

```

注册事件和监听器:

```php
class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
	
        PostCommented::class => [
            PostCommentedNotification::class
        ]
    ];
}
```

继续完善代码：

```php
<!-- app/Events/PostCommented.php -->

<?php>

namespace App\Events;

use App\Models\Comment;

class PostCommented
{
    use Dispatchable, InteractsWithSockets, SerializesModels;
		
    public Comment $comment;

    public function __construct(Comment $comment)
    {
        $this->comment = $comment;
    }
}
```

```php
<!-- app/Listeners/PostCommentedNotification.php -->

<?php

namespace App\Listeners;

use App\Events\PostCommented;

class PostCommentedNotification
{
    public function handle(PostCommented $event)
    {
        $comment = $event->comment;

        // TODO 发送邮件通知
    }
}

```

保存评论时触发事件：

```php
<!-- app/Http/Controllers/PostController.php -->

<?php

namespace App\Http\Controllers;

use App\Events\PostCommented;
use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function comment($id, Request $request)
    {
        $post = Post::findOrFail($id);

        $comment = $post->comment()->create([
            'body' => $request->body
        ]);

	// 触发事件
        event(new PostCommented($comment));

        return redirect()->route('post.show', $id);
    }
}

```

Demo：https://github.com/hefengbao/laravel-demo