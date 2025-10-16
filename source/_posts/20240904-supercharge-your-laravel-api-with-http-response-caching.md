---
title: " 使用 HTTP 响应缓存加速 Laravel API"
date: 2024-09-04 17:17:17
tags:
  - laravel
  - laravel-api
categories: Laravel
---
HTTP 响应（response）可以通过设置 [Cache-Control](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control) 和 [ETag](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/ETag) 响应头（header）实现缓存。

Laravel API 中的实例：

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache;

class ProductController extends Controller
{
    public function show($id, Request $request)
    {
        $product = Cache::remember("product:{$id}", 3600, function () use ($id) {
            return Product::findOrFail($id);
        });

        return response()->json($product)
            ->header('Cache-Control', 'public, max-age=3600')
            ->setEtag(md5($product->updated_at));
    }
}
```

1. 使用 Laravel 的 `Cache::remember()` 方法实现服务器端缓存，实践中应该是 `redis` 等实现，缓存 3600 秒。
2. 使用 `Cache-Control` header 设置响应缓存。
3. 使用 `Etag` header 判断响应缓存是否过期。

列表 `Etag` 设置的技巧：

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache;

class ProductCatalogController extends Controller
{
    public function index(Request $request)
    {
        $page = $request->get('page', 1);
        $perPage = 20;

        $cacheKey = "product_catalog:page:{$page}";

        $products = Cache::remember($cacheKey, 3600, function () use ($page, $perPage) {
            return Product::with('category')
                ->where('status', 'active')
                ->orderBy('name')
                ->paginate($perPage);
        });

        return response()->json($products)
            ->header('Cache-Control', 'public, max-age=3600')
            ->setEtag(md5($products->lastPage() . $products->total() . $products->currentPage()));
    }

    public function updateProduct($id, Request $request)
    {
        $product = Product::findOrFail($id);
        $product->update($request->all());

        // Invalidate cache for all pages
        Cache::flush();

        return response()->json(['message' => 'Product updated successfully']);
    }
}
```

参考：

https://www.harrisrafto.eu/supercharge-your-laravel-api-with-http-response-caching
