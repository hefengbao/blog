---
title: Laravel 方法：Str::replaceArray()
date: 2024-09-05 17:17:17
tags:
  - laravel
  - laravel-str
categories: Laravel
---
`Str:：replaceArray()` 方法是 `Illuminate\Support\Str` 类的一部分。它允许你使用数组元素替换字符串中的特定元素（占位符）。这对于模板和动态内容生成特别有用。

基本使用：

```php
use Illuminate\Support\Str;

$template = 'The event will take place on ?, at ?';
$replaced = Str::replaceArray('?', ['Monday', '10 AM'], $template);

echo $replaced; // Output: The event will take place on Monday, at 10 AM
```

实际案例，邮件通知；

```php
use Illuminate\Support\Str;

class NotificationController extends Controller
{
    public function sendEventNotification($event)
    {
        $template = 'Hello ?, your event ? is scheduled for ? at ?.';
        $replacements = [
            $event->user->name,
            $event->name,
            $event->date->format('l'),
            $event->time->format('h:i A'),
        ];

        $message = Str::replaceArray('?', $replacements, $template);

        // Send the email using the generated message
        Mail::to($event->user->email)->send(new EventNotification($message));

        return response()->json(['status' => 'Notification sent successfully.']);
    }
}
```