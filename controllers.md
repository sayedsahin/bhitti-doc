---
layout: default
title: Controllers
---

# Controllers

Controllers normally live in `app/Controllers/` and are resolved through Bhitti's service container only after route middleware succeeds.

```php
<?php

declare(strict_types=1);

namespace App\Controllers;

use Bhitti\Http\Response;

final class UserController extends Controller
{
    public function index(): Response
    {
        $users = db()
            ->table('users')
            ->select('id', 'name', 'email')
            ->get();

        return response()->json(['users' => $users]);
    }
}
```

## Constructor injection

Concrete constructor dependencies are autowired:

```php
final class ReportController extends Controller
{
    public function __construct(private ReportService $reports)
    {
    }
}
```

Bind interfaces or custom implementations in container configuration when automatic construction is not enough.

## Route parameters

Route parameters are passed to the controller method:

```php
public function show(int $id): Response
{
    $user = db()->table('users')->find($id);

    return response()->json(['user' => $user]);
}
```

Bhitti does not inject the Request object into controller methods. Use `request()` when needed.

## Returning views

`view()` returns the rendered string:

```php
public function index(): string
{
    return view('welcome', ['title' => 'Bhitti']);
}
```
