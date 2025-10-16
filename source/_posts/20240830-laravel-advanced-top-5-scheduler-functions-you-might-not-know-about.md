---
title: "Laravel Tips: 5 个调度方法"
date: 2024-08-30 17:17:17
tags:
  - laravel
  - laravel-tips
categories: Laravel
---
## 1. skip() & when()

If you want your scheduled task to execute only when some condition is `true`, use `when()` to set such conditions inline:

```php
$schedule->command('your:command')->when(function () {
    return some_condition();
});
```

`skip()` is the exact opposite of the `when()` method. If the skip method returns `true`, the scheduled task will not be executed:

```php
$schedule->command('emails:send')->daily()->skip(function(){
    return Calendar::isHolidauy();
});
```

## 2. withoutOverlapping()

You may be running a critical job that should only have one instance running at a time. That's where `withoutOverlapping()` ensures that a scheduled task won't overlap, preventing potential conflicts.

```php
$schedule->command('your:command')->withoutOverlapping();
```

## 3. thenPing()

After executing a task, you might want to ping a URL to notify another service or trigger another action. `thenPing()` lets you do just that seamlessly.

```php
$schedule->command('your:command')->thenPing('http://example.com/webhook');
```

## 4. runInBackground()

If you want your scheduled task to run in the background without holding up other processes. `runInBackground()` will help you do this:

```php
$schedule->command('your:command')->runInBackground();
```

## 5. evenInMaintenanceMode()

You can guess what it does by its name. You can execute scheduled tasks even when your application is in maintenance mode.

```php
$schedule->command('your:command')->evenInMaintenanceMode();
```



https://backpackforlaravel.com/articles/tips-and-tricks/laravel-advanced-top-5-scheduler-functions-you-might-not-know-about