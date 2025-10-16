---
title: Filament 表单复用
date: 2024-08-28 17:17:17
tags:
  - filament
categories: Filament
---
## 创建表单组件

### Step 1：定义表单组件

例如，你需要创建用户管理的表单，那么应该在 `app/Filament/Components ` 目录下创建 `UserForm.php` 文件：

```php
<?php

namespace App\Filament\Components;

use Filament\Forms\Components\{Select, TextInput};

class UserForm
{
    public static function schema(): array
    {
        return [
            TextInput::make('name')
                ->required()
                ->label('Name'),
            TextInput::make('email')
                ->email()
                ->required()
                ->label('Email Address'),
            Select::make('role')
                ->options([
                    'admin' => 'Administrator',
                    'user' => 'User',
                ])
                ->required()
                ->label('Role'),
            // Add more fields as needed...
        ];
    }
}
```

### Step 2：使用组件

```php
<?php

namespace App\Filament\Resources;

use App\Models\User;
use Filament\Forms\Form;
use Filament\Resources\Resource;
use App\Filament\Components\UserForm;
use App\Filament\Resources\UserResource\Pages;

class UserResource extends Resource
{
    protected static ?string $model = User::class;

    protected static ?string $navigationIcon = 'heroicon-o-user';

    public static function form(Form $form): Form
    {
        return $form->schema(
            UserForm::schema(),
        );
    }

    public static function getPages(): array
    {
        return [
            'index' => Pages\ListUser::route('/'),
            'create' => Pages\CreateUser::route('/create'),
            'view' => Pages\ViewUser::route('/{record}'),
            'edit' => Pages\EditUser::route('/{record}/edit'),
        ];
    }
}
```

通过调用 `UserForm::schema()`,你可以在项目的任何地方使用该表单。

## 适配不同的场景表单组件

### 更新表单组件

```php
<?php

namespace App\Filament\Components;

use Filament\Forms\Components\{Select, TextInput};

class UserForm
{
    public static function schema(array $options = []): array
    {
        return [
            TextInput::make('name')
                ->required()
                ->hidden($options['nameHidden'] ?? false)
                ->label('Name'),
            TextInput::make('email')
                ->email()
                ->required()
                ->label('Email Address'),
            Select::make('role')
                ->options([
                    'admin' => 'Administrator',
                    'user' => 'User',
                ])
                ->required()
                ->label('Role'),
            // Add more fields as needed...
        ];
    }
}
```

### Step 2：使用组件是传递参数

```php
<?php

public static function form(Form $form): Form
{
    return $form->schema(
        UserForm::schema(options: [
            'nameHidden' => true,
        ]),
    );
}

```


参考：https://www.luckymedia.dev/blog/reusing-filament-forms