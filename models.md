---
layout: default
title: Models
---

# Models

Bhitti models are lightweight Query Builder subclasses. They provide reusable defaults without introducing a full Active Record lifecycle.

```php
<?php

declare(strict_types=1);

namespace App\Models;

use Bhitti\Database\QueryBuilder;

final class User extends QueryBuilder
{
    protected string $defaultTable = 'users';

    protected array $defaultSelect = [
        'name',
        'email',
    ];

    // protected ?string $defaultConnection = 'sqlite';
}
```

## Start a fresh query

```php
$users = User::query()
    ->where('email_verified', 1)
    ->get();
```

`query()` returns fresh builder state using the model's default table, select list, and optional connection.

## Override the select list

```php
$user = User::query()
    ->select('id', 'name', 'email')
    ->where('id', 5)
    ->first();
```

## Scope of the model layer

Bhitti models intentionally do not provide automatic relationships, dirty tracking, events, or entity persistence. Use the Query Builder directly when that is clearer.

## Global DB model

For queries that do not need an application model, use Bhitti's global `DB` model:

```php
use Bhitti\Database\DB;

$users = DB::query()
    ->table('users')
    ->select('id', 'name')
    ->get();
```

`DB::query()` returns fresh Query Builder state. Application models such as `User::query()` are useful when you want model-specific defaults; `DB::query()` is the general-purpose model.
