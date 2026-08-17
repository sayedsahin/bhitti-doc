---
layout: default
title: Request Lifecycle
---

# Request Lifecycle

Bhitti keeps the hot path direct and avoids starting stateful services before they are needed.

## 1. Entry point

`public/index.php` loads the application and captures the request:

```php
$app = require dirname(__DIR__) . '/bootstrap/app.php';
$app->run(Request::capture());
```

## 2. Application bootstrap

The application loads configuration, services, the container, router, middleware kernel, and route dispatcher. In production, cached configuration and cached routes are used when available.

## 3. Kernel middleware

The kernel executes `middleware.kernel.web` or `middleware.kernel.api` **before route matching**.

These middleware must remain stateless because the session is not configured at this stage. Header/CORS-style middleware belongs here.

## 4. Route matching

FastRoute resolves the request.

- `NOT_FOUND` returns 404.
- `METHOD_NOT_ALLOWED` returns 405 and an `Allow` header.
- `FOUND` continues to route execution.

404/405 requests do not start route middleware or a web session.

## 5. Matched route execution

For a matched **web** route, the configured session driver is registered. If sessions are disabled, Bhitti uses the null session driver. API routes do not configure PHP sessions.

Bhitti then merges:

1. route-level global middleware from `config/middleware.php`, and
2. route/controller middleware collected with the route.

Only after middleware succeeds is the controller resolved from the container.

## 6. Controller arguments

Route scalar parameters are validated against built-in parameter types captured by the router (`int`, `float`, `bool`, and similar built-ins). Invalid typed route input receives a 400 response.

## 7. Controller result

- A `Response` object is sent.
- A string is emitted as the response body.
- `view()` returns a string, so controllers can simply `return view(...)`.
