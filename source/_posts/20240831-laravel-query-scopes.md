---
title: Laravel 查询作用域（Query Scopes）
date: 2024-08-31 17:17:17
tags:
  - laravel
categories: Laravel
---
## 什么是查询作用域（Query Scopes）?

查询作用域让你以复用的方式为模型查询（Eloquent queries）定义条件约束。通常在模型（Model）中一匿名方法的形式定义、或者定义一个继承自 `Illuminate\Database\Eloquent\Scope` 接口的类（class）。

查询作用域的分类：

- 局部作用域：你需要在查询中手动调用此方法。
- 全局作用域：自动应用到你的查询中。

如果使用过 Laravel 的软删除（soft delete）功能，默认会在模型查询中添加全局约束 `whereNull('deleted_at') `，还提供了局部作用域 `withTrashed` 或 `onlyTrashed`。

## 局部作用域（Local Query Scopes）

假设我们构建一个博客应用，`\App\Models\Article` 模型中有一个可为 `null` 的 `published_at` 字段来存储发布时间，如果 `published_at` 的时间在当前时间以前，则认为已发布，如果为 `null` 或者则当前时间之后，则认为未发布。

获取已发布的文章；

```php
use App\Models\Article;
 
$publishedPosts = Article::query()
    ->where('published_at', '<=', now())
    ->get();
```

获取未发布的文章：

```php
use App\Models\Article;
use Illuminate\Contracts\Database\Eloquent\Builder;
 
$unpublishedPosts = Article::query()
    ->where(function (Builder $query): void {
        $query->whereNull('published_at')
            ->orWhere('published_at', '>', now());
    })
    ->get();
```

使用`局部作用域`来优化上面的逻辑，在 `\App\Models\Article` 中定义局部作用域：

```php
declare(strict_types=1);
 
namespace App\Models;
 
use Illuminate\Contracts\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
 
final class Article extends Model
{
    public function scopePublished(Builder $query): Builder
    {
        return $query->where('published_at', '<=', now());
    }
 
    public function scopeNotPublished(Builder $query): Builder
    {
        return $query->where(function (Builder $query): Builder {
            return $query->whereNull('published_at')
                ->orWhere('published_at', '>', now());
        });
    }
 
    // ...
}
```

那么获取已发布文章、未发布文章的逻辑可以改写为：

```php
use App\Models\Article;
 
$publishedPosts = Article::query()
    ->published() // 这里手动调用
    ->get();
 
$unpublishedPosts = Article::query()
    ->notPublished() // 这里手动调用
    ->get();
```

**注意：在 `Article` 模型中定义的方法名为 `scopePublished`、`scopeNotPublished`,然后使用的时候是 `published()`、`>notPublished()`。 **

## 全局作用域（Global Query Scopes）

假设我们构建的是一个多租户（multi-tenant）的博客系统，用户只能看到他们所属组的文章，可能的查询：

```php
use App\Models\Article;
 
$articles = Article::query()
    ->where('team_id', Auth::user()->team_id)
    ->get();
```

对于这样的系统，需每次添加 `where('team_id', Auth::user()->team_id)` 约束，简化的方法就是添加全局作用域。有两种方式，一种是通过 `php artisan make:scope` 命令创建单独的类；二是使用匿名方法；

### 通过 `php artisan make:scope` 创建全局作用域

```bash
php artisan make:scope TeamScope
```
将会创建 ` app/Models/Scopes/TeamScope.php ` 文件：

```php
declare(strict_types=1);
 
namespace App\Models\Scopes;
 
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Scope;
use Illuminate\Support\Facades\Auth;
 
final readonly class TeamScope implements Scope
{
    /**
     * Apply the scope to a given Eloquent query builder.
     */
    public function apply(Builder $builder, Model $model): void
    {
        $builder->where('team_id', Auth::user()->team_id);
    }
}
```

注册该全局作用域，有两种方式：

1. 使用 `Illuminate\Database\Eloquent\Attributes\ScopedBy` 属性：

```php
declare(strict_types=1);
 
namespace App\Models;
 
use App\Models\Scopes\TeamScope;
use Illuminate\Database\Eloquent\Attributes\ScopedBy;
use Illuminate\Database\Eloquent\Model;
 
#[ScopedBy(TeamScope::class)]
final class Article extends Model
{
    // ...
}
```

2. 在模型的 `booted ` 方法中使用 `addGlobalScope` 方法

```php
declare(strict_types=1);
 
namespace App\Models;
 
use App\Models\Scopes\TeamScope;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
 
final class Article extends Model
{
    use HasFactory;
 
    protected static function booted(): void
    {
        static::addGlobalScope(new TeamScope());
    }
 
    // ...
}
```

查询文章的逻辑可以简化为：

```php
use App\Models\Article;
 
$articles = Article::query()->get();
```

假设 `team_id` 为 1 ，那么生成的 sql 如下：

```sql
select * from `articles` where `team_id` = 1
```

可以看到，会自动为模型查询添加全局作用域。


### 使用匿名方法的方式创建全局作用域

直接在模型的 `booted` 方法中通过匿名方法：

```php
declare(strict_types=1);
 
namespace App\Models;
 
use Illuminate\Contracts\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Facades\Auth;
 
final class Article extends Model
{
    protected static function booted(): void
    {
        static::addGlobalScope('team_scope', static function (Builder $builder): void {
            $builder->where('team_id', Auth::user()->team_id);
        });
    }
 
    // ...
}
```

## 忽略全局作用域

由于全局作用域在模型查询是默认添加的，但是在某些场景下，不需要全局作用域定义的逻辑，可以使用 `withoutGlobalScopes` 方法忽略全局作用域：

```php
use App\Models\Article;
 
$articles = Article::query()->withoutGlobalScopes()->get();
```

默认忽略该模型定义的所有全局作用域， 可以指定忽略特定的作用域：

```php
use App\Models\Article;
use App\Models\Scopes\TeamScope;
 
$articles = Article::query()
    ->withoutGlobalScopes([
        TeamScope::class,
        'another_scope',
    ])->get();
```

上述代码同时演示了两种方法创建的全局作用域如何指定。

**注意：全局作用域仅在模型查询时添加，如果是用数据库查询，例如 `$articles = DB::table('articles')->get();` ,并不会添加全局作用域提供的逻辑。 **

测试用例写法参考原文。

参考：https://laravel-news.com/query-scopes
