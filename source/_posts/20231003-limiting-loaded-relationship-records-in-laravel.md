---
title: LaravelTips：在 Laravel 中加载限制数量的关联数据并避免 N+1问题
date: 2023-10-03 17:17:17
tags:
  - laravel
  - laravel-tips
categories: Laravel
---
标题可能有点绕，比如微博首页的消息流，每条消息最多显示5条热门评论，Laravel 关联查询如何实现？

```php
$limit = 5;

$users = User::query()
    ->addSelect([
        'highest_rated_comment_ids' => Comment::query()
            ->selectRaw("SUBSTRING_INDEX(GROUP_CONCAT(comments.id order by rating desc, ','), ',', {$limit})")
            ->whereColumn('comments.user_id', 'users.id')
            ->limit($limit)
    ])
    ->get();
```

```sql
select
    `users`.*,
    (
    select
        SUBSTRING_INDEX(GROUP_CONCAT(comments.id order by rating desc, ','), ',', 5)
    from
        `comments`
    where
        `comments`.`user_id` = `users`.`id`
    limit 5) as `highest_rated_comments`
from
    `users`
```

```php
$highestRatedCommentIds = $users->pluck('highest_rated_comment_ids')
    ->map(function (string $commentIds) {
        return explode(',', $commentIds);
    })
    ->flatten();

// $highestRatedCommentIds = [27, 4, 39, 21, 107, ...];

$highestRatedComments = $this->getQueryBuilder()
    ->whereIn('id', $highestRatedCommentIds)
    ->get();
```

```php
foreach ($users as $user) {
    // Turn the highest_rated_comment_ids property from the user into an array
    $userHighestRatedCommentIds = explode(',', $user->highest_rated_comment_ids);

    // Pull the comments that relate to this user out of the collection of all comments
    // sortBy ensures they are added in the same order as highest_rated_comment_ids i.e. descending rating order
    $userHighestRatedComments = $highestRatedComments
        ->whereIn('id', $userHighestRatedCommentIds)
        ->sortBy(fn (Comment $comment) => array_flip($userHighestRatedCommentIds)[$comment->id])
        ->values();

    // Add the comments to the user
    $user->setRelation('highest_rated_comments', $userHighestRatedComments);
}
```


[LIMITing Loaded Relationship Records While Avoiding N+1 in Laravel](https://ides.dev/notes/limiting-loaded-relationship-records-in-laravel)