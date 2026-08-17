---
layout: default
title: Middleware
---

# Middleware

Bhitti middleware is deliberately simple: `handle()` returns `null` to continue or a `Response` to stop the request.

```php
<?php

declare(strict_types=1);

namespace App\Middlewares;

use Bhitti\Http\Middleware\MiddlewareInterface;
use Bhitti\Http\Response;

final class VerifiedEmail implements MiddlewareInterface
{
    public function handle(): ?Response
    {
        $user = auth()->user();

        if (!$user || !$user->email_verified) {
            return response()->json([
                'message' => 'Email verification required.',
            ], 403);
        }

        return null;
    }
}
```

## Two global middleware levels

`config/middleware.php` separates middleware by when it should run:

```php
return [
    'kernel' => [
        'web' => [WebHeaders::class],
        'api' => [ApiHeaders::class],
    ],

    'route' => [
        'web' => [
            RateLimit::class,
            RememberMe::class,
            Csrf::class,
        ],
        'api' => [
            RateLimit::class,
        ],
    ],
];
```

### Kernel-level middleware

Kernel middleware runs **before route matching** for every request. Keep it stateless. The session is not configured yet.

Good uses include response/security headers and other request-level behavior that must also apply to 404/405 responses.

### Route-level middleware

Route-level global middleware runs only after a route is found. Web sessions are configured immediately before this stage, so session-dependent middleware belongs here.

This means unknown routes do not unnecessarily touch session storage or route-level rate limiting.

## Route-specific middleware

```php
$route->get('/dashboard', [
    DashboardController::class,
    'index',
    [Authenticated::class],
]);
```

The route-level global middleware runs first, followed by route/controller middleware.

## Middleware arguments

```php
[RoleMiddleware::class, ['admin']]
```

Bhitti resolves middleware through the container and passes supplied arguments with `makeWith()`.

## Controller middleware attributes

Bhitti supports repeatable PHP attributes for controller-level middleware:

```php
use App\Middlewares\RoleMiddleware;
use Bhitti\Http\Middleware\Attributes\Middleware;

#[Middleware(RoleMiddleware::class, ['user'])]
final class ProfileController extends Controller
{
    // Every routed action in this controller receives RoleMiddleware.
}
```

Method-level middleware applies only to the decorated action:

```php
use App\Middlewares\Guest;
use Bhitti\Http\Middleware\Attributes\Middleware;

#[Middleware(Guest::class)]
public function registrationProcess(): Response
{
    // ...
}
```

The attribute is repeatable:

```php
#[Middleware(Authenticated::class)]
#[Middleware(RoleMiddleware::class, ['admin'])]
public function dashboard(): string
{
    return view('admin.dashboard');
}
```

Middleware arguments are passed as the attribute's second argument:

```php
#[Middleware(RoleMiddleware::class, ['admin'])]
```

For a matched route, execution order is:

1. route-level global middleware,
2. route-specific middleware,
3. controller class attributes,
4. controller method attributes.

Controller attributes are discovered during route registration and stored with the prepared route handler. They are therefore included in the route cache. After changing an attribute, rebuild the cache with `php run route:cache`.
