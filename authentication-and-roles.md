---
layout: default
title: Authentication and Roles
---

# Authentication and Roles

The starter application includes a small session-based authentication layer under `app/Supports/` and middleware under `app/Middlewares/`. These are application components built on Bhitti's session, request context, database, and middleware APIs.

## User resolver

`bootstrap/services.php` registers the auth resolver through `AuthResolver`. The resolver maps an authenticated user ID to the application user object/row.

## Login

```php
use App\Supports\Auth;

Auth::login((int) $user->id);
```

Login regenerates the session ID and stores `auth_user_id`.

## Auth state

```php
Auth::check();
Auth::id();
Auth::user();
Auth::viaRemember();
```

Resolved auth state is cached in `RequestContext` for the current request.

## Logout

```php
Auth::logout();
```

Logout destroys the session, removes the remember-token cookie, and clears request-scoped auth state.

The starter application's logout route is `POST /logout` and is protected by `Authenticated` middleware.

## Route protection

```php
$route->get('/dashboard', [
    DashboardController::class,
    'index',
    [Authenticated::class],
]);
```

Use `Guest` for routes that should be unavailable to authenticated users.

## Roles

The starter application stores roles in `roles` and assignments in `user_roles`. Role results are cached for the current request.

```php
use App\Supports\Role;

Role::has('admin');
Role::any(['admin', 'editor']);
Role::all(['admin', 'verified']);
Role::userRoles();
```

Assign/remove:

```php
Role::assign($userId, 'admin');
Role::remove($userId, 'admin');
```

The current `user_roles` migration uses a composite unique constraint to prevent duplicate user/role pairs.

## Role middleware

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
