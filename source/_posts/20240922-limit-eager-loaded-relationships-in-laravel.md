---
title: Laravel 预加载（Eager Loaded ）限定条数
date: 2024-09-22 17:17:17
tags:
  - laravel
categories: Laravel
---

![](https://hefengbao.github.io/assets/images/202409221338691.png)

常见的用例：评论列表中每条评论下最多显示三条回复。

下面的代码示例是查询每个作者最近的三篇文章。

```php
use App\Models\Comment;
use App\Models\Author;
use Illuminate\Contracts\Database\Eloquent\Builder;
 
Author::query()
    ->with([
        'posts' => fn (Builder $query): Builder => $query->limit(3),
    ])
    ->get();
```

生成的 sql:

```sql
SELECT * FROM `authors`
```

```sql
SELECT *
FROM
    (
        SELECT
            *,
            row_number() OVER (PARTITION BY `posts`.`author_id`) AS `laravel_row`
        FROM `posts`
        WHERE `posts`.`author_id` IN (1, 2, 3, 4, 5)
    ) AS `laravel_table`
WHERE `laravel_row` <= 3
ORDER BY `laravel_row`
```

参考：
https://ashallendesign.co.uk/blog/limit-eager-loaded-relationships