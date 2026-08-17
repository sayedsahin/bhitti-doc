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

Controller classes or methods can declare Bhitti's middleware attribute. Collected controller middleware is merged into the same matched-route middleware list.

## Route cache

```bash
php run route:cache
```

The generated FastRoute cache is stored at:

```text
storage/cache/route.cache.php
```
