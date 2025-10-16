---
title: Laravel loadMissing()
date: 2025-05-28 17:17:17
tags:
  - laravel
categories: Laravel
---
Laravel 的 `loadMissing` 方法提供了一种灵活的方式，对现有模型或集合进行预加载。该方式避免了 N+1 的查询问题，同时允许你只在需要时加载关联。

当使用可选的内容或仪表板构建 API 时，此功能尤其有价值，因为不同的部分需要不同的关联数据。

```php
$post->loadMissing(['comments', 'author']);
// With constraints
$post->loadMissing(['comments' => function($query) {
    $query->latest()->take(5);
}]);
```

以下是仪表盘数据加载器的示例：

```php
<?php
 
namespace App\Http\Controllers;
 
use App\Models\Dashboard;
use Illuminate\Http\Request;
 
class DashboardController extends Controller
{
    public function show(Request $request, Dashboard $dashboard)
    {
        // Load base relationships
        $dashboard->loadMissing([
            'widgets',
            'owner',
        ]);
 
        // Conditionally load additional data
        if ($request->section === 'analytics') {
            $dashboard->loadMissing([
                'widgets.viewHistory' => function($query) {
                    $query->whereBetween('viewed_at', [
                        now()->subDays(30),
                        now()
                    ]);
                },
                'widgets.interactions'
            ]);
        }
 
        if ($request->section === 'sharing') {
            $dashboard->loadMissing([
                'sharedUsers',
                'shareLinks' => function($query) {
                    $query->where('expires_at', '>', now());
                }
            ]);
        }
        return $dashboard;
    }
}
```

`loadMissing` 方法智能地仅加载所需的关联：

```php
// GET /dashboard/1?section=analytics
{
    "id": 1,
    "name": "Sales Overview",
    "widgets": [
        {
            "id": 1,
            "type": "chart",
            "viewHistory": [
                {
                    "viewed_at": "2024-02-01T10:30:00Z",
                    "views": 150
                }
            ],
            "interactions": [
                {
                    "type": "filter_change",
                    "timestamp": "2024-02-01T11:20:00Z"
                }
            ]
        }
    ]
}
```

`LoadMissing` 提供了一个有效的方式来管理管理加载、优化数据库查询，同时保留了代码的灵活性。

[Laravel 动态关联加载](https://www.tubring.cn/articles/laravel-loadMissing)