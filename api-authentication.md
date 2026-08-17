---
layout: default
title: API Authentication
---

# API Authentication

The starter application includes bearer-token authentication for `/api/auth/*` routes.

Current routes include:

```text
POST /api/auth/login
POST /api/auth/register
POST /api/auth/forgot
GET  /api/auth/verify/{token}
POST /api/auth/logout
GET  /api/auth/profile
```

Protected routes use `BearerAuth` middleware.

## Client header

```http
Authorization: Bearer RAW_TOKEN
```

The application returns the raw token to the client and stores a SHA-256 hash in the database rather than storing the raw token.

## Protect an API route

```php
$route->get('/api/private', [
    PrivateController::class,
    'index',
    [BearerAuth::class],
]);
```

`BearerAuth` validates the token, resolves the user, and places the authenticated state into the application's request-scoped auth support without starting a PHP web session.

## API middleware

API requests use `middleware.kernel.api` before routing and `middleware.route.api` after a route is found. In the starter configuration this applies API headers globally and route-level rate limiting to matched API routes.
