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

## Controller middleware attributes

Bhitti supports middleware directly on controller classes and methods through the repeatable `#[Middleware]` PHP attribute.

Import the attribute and middleware class:

```php
use App\Middlewares\Guest;
use App\Middlewares\RoleMiddleware;
use Bhitti\Http\Middleware\Attributes\Middleware;
```

### Class-level middleware

A class-level attribute applies to every routed method on that controller:

```php
#[Middleware(RoleMiddleware::class, ['user'])]
final class ProfileController extends Controller
{
    public function index(): string
    {
        return view('profile.index');
    }
}
```

### Method-level middleware

A method-level attribute applies only to that action:

```php
final class AuthController extends Controller
{
    #[Middleware(Guest::class)]
    public function registrationProcess(): Response
    {
        // ...
    }
}
```

The attribute is repeatable, so multiple middleware may be declared:

```php
#[Middleware(Authenticated::class)]
#[Middleware(RoleMiddleware::class, ['admin'])]
public function dashboard(): string
{
    return view('admin.dashboard');
}
```

Controller attributes are collected when routes are registered. They are stored in the prepared route handler and therefore included in the route cache. Rebuild the route cache after changing controller middleware attributes:

```bash
php run route:cache
```

For a matched route, middleware executes in this order:

1. route-level global middleware,
2. route-specific middleware declared in `config/routes.php`,
3. controller class-level attributes,
4. controller method-level attributes.

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
