---
layout: default
title: Routing
---

# Routing

Routes are defined in `config/routes.php` using FastRoute's route collector extended by Bhitti.

## Basic routes

```php
$route->get('/users', [UserController::class, 'index']);
$route->post('/users', [UserController::class, 'store']);
```

Other FastRoute methods such as `put`, `patch`, and `delete` are available through the collector.

## Route parameters

```php
$route->get('/users/{id:\\d+}', [UserController::class, 'show']);
```

```php
public function show(int $id): Response
{
    return response()->json(['id' => $id]);
}
```

Bhitti records built-in controller parameter types in the route definition and validates matched values before invoking the controller.

## Route groups

```php
$route->addGroup('/api', function () use ($route) {
    $route->get('/welcome', [WelcomeController::class, 'apiIndex']);
});
```

Groups can be nested.

## Route middleware

Put route-specific middleware in the third element of the handler array:

```php
$route->get('/dashboard', [
    DashboardController::class,
    'index',
    [Authenticated::class],
]);
```

Parameterized middleware:

```php
$route->get('/admin', [
    AdminController::class,
    'index',
    [
        Authenticated::class,
        [RoleMiddleware::class, ['admin']],
    ],
]);
```

## Invokable controllers

A class name may be used directly when it implements `__invoke()`:

```php
$route->get('/health', HealthController::class);
```

## Controller middleware attributes

Controller classes and methods may declare middleware with Bhitti's repeatable PHP attribute:

```php
use App\Middlewares\RoleMiddleware;
use Bhitti\Http\Middleware\Attributes\Middleware;

#[Middleware(RoleMiddleware::class, ['user'])]
final class ProfileController extends Controller
{
    #[Middleware(VerifiedEmail::class)]
    public function index(): string
    {
        return view('profile.index');
    }
}
```

Class-level attributes apply to every routed method in the controller. Method-level attributes apply only to that method. Attribute arguments use the same middleware argument format as route middleware.

During route registration Bhitti merges middleware in this order:

```text
route-specific middleware
→ controller class attributes
→ controller method attributes
```

At request time, route-level global middleware from `config/middleware.php` runs before that prepared list.

## Route cache

```bash
php run route:cache
```

The generated FastRoute cache is stored at:

```text
storage/cache/route.cache.php
```

Controller class/method `#[Middleware]` attributes and controller parameter type metadata are collected during route registration and stored in the prepared route handler. Rebuild the route cache whenever routes or controller middleware attributes change.
