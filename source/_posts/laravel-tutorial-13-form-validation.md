---
title: Laravel 入门：13-表单验证
date: 2022-06-09 17:17:17
tags:
  - laravel
categories: Laravel
---
一个重要的原则，不要用户提交的信任内容，这意味着需要对用户提交的内容做验证，以避免出现不必要的错误。

最直接的做法就是在控制器中直接验证， `Illuminate\Http\Request` 提供了 `validate` 方法：

``` php
<!-- app/Http/Controllers/UserController.php  -->

<?php 
namespace App\Http\Controllers; 
	
use App\Models\User; 
use Illuminate\Http\Request; 
	
class UserController extends Controller
{
	public function store(Request $request)
	{
		$this->validate($request, [
			'name' => 'required|max:10',
			'email' => 'required|email:rfc,dns|unique:users'
		]);
			
		// 或者使用数组而不是 | 分割
		$this->validate($request, [
			'name' => ['required', 'max:10'],
			'email' => ['required', 'email:rfc,dns', 'unique:users']
		]);
	}
}
```

验证失败时，应用会返回到表单界面，如果不做处理，将会是空白的表单，用户需要重新填写，显然不够友好，较好的做法是，把旧的数据显示出来并提示哪里验证不通过：

``` php
<!-- resources/views/users/create.blade.php --> 

<div class="card">
    <div class="card-body">
        @if($errors->any())
            <div class="alert alert-danger">
                <ul>
                    @foreach ($errors->all() as $error)
                        <li>{{ $error }}</li>
                    @endforeach
                </ul>
            </div>
        @endif
        <form action="{{ route('users.store') }}" method="POST">
            @csrf
            <div class="mb-3">
                <label for="name" class="form-label">用户名</label>
                <input type="text" class="form-control" id="name" name="name" value="{{ old('name') }}" required>
            </div>
            <div class="mb-3">
                <label for="email" class="form-label">邮箱</label>
                <input type="email" class="form-control" id="email" name="email" value="{{ old('email') }}" required>
            </div>
            <div class="mb-3">
                <button class="btn btn-primary" type="submit">保存</button>
            </div>
        </form>
    </div>
</div>
```

注意 `old()` 和 `errors` 的使用 。

还可以定义单独的验证文件：

``` bash
php artisan make:request UserCreateRequest
```

``` php
<!-- app/Http/Requests/UserCreateRequest.php-->

class UserCreateRequest extends FormRequest
{
    public function authorize()
    {
        // 这里要改为 true
        return true;
    }
		
    public function rules()
    {
        return [
            'name' => ['required', 'max:10'],
            'email' => ['required', 'email:rfc,dns', 'unique:users']
        ];
    }

    // 自定义验证错误提示信息
    public function messages()
    {
        return [
            'name.required' => '用户名不能为空',
            'name.max' => '用户名不能超过10个字符',
            'email.required' => '邮箱不能为空',
            'email.email' => '不是有效的邮箱',
            'email.unique' => '该邮箱已注册'
        ];
    }
}
```

``` php
<!-- app/Http/Controllers/UserController.php  -->

public function store(UserCreateRequest $request)
{
	// 保存逻辑
}
```

Demo：https://github.com/hefengbao/laravel-demo