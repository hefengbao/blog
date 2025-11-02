---
title: 基于 Laravel 的 Dimension 规则进行图像大小验证
date: 2025-11-02 18:00:47
tags:
  - laravel
categories: Laravel
permalink: 20251102-image-dimension-validation-rule
---
Laravel 通过 Dimensions 规则提供了强大的图像验证功能，为应用的媒体上传提供了对图像大小和比例的精细控制。

下例中的基本实现展示了规则的灵活性：

```php
use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\Rule;
 
// Basic validation
$validator = Validator::make($request->all(), [
    'profile_photo' => [
        'required',
        Rule::dimensions()
            ->minWidth(400)
            ->minHeight(400)
            ->maxWidth(2000)
            ->maxHeight(2000)
    ]
]
```

以下是如何再媒体管理系统中实现复杂图片验证的策略：

```php
class MediaController extends Controller
{
    public function upload(Request $request)
    {
        $imageType = $request->input('type', 'standard');
 
        $rules = [
            'image' => array_merge(
                ['required', 'image', 'max:10240'], // 10MB max
                $this->getDimensionRules($imageType)
            )
        ];
 
        $request->validate($rules);
 
        // Process and store the validated image
        $path = $request->file('image')->store('media/' . $imageType);
 
        return response()->json([
            'success' => true,
            'path' => $path
        ]);
    }
 
    protected function getDimensionRules(string $type): array
    {
        return match($type) {
            'thumbnail' => [
                Rule::dimensions()
                    ->width(300)
                    ->height(300)
            ],
            'hero' => [
                Rule::dimensions()
                    ->minWidth(1200)
                    ->minHeight(600)
                    ->maxHeight(800)
                    ->ratio(2)
            ],
            'gallery' => [
                Rule::dimensions()
                    ->minWidth(800)
                    ->minHeight(600)
                    ->maxWidth(3000)
                    ->maxHeight(2000)
            ],
            default => [
                Rule::dimensions()
                    ->minWidth(400)
                    ->minHeight(400)
            ]
        };
    }
}
```

Dimensions 规则可确保整个应用的最佳图像质量，同时防止因上传大小不当而导致的存储和带宽问题。


[基于 Laravel 的 Dimension 规则进行图像大小验证 | 日思录](https://tubring.cn/articles/image-dimension-validation-rule)