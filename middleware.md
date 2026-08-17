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

Bhitti also supports its `#[Middleware(...)]` attribute on controllers/methods. These entries are collected during route registration and executed in the same matched-route middleware list.
