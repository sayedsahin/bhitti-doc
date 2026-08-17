---
layout: default
title: Rate Limiting
---

# Rate Limiting

Bhitti supports file, APCu, Redis, and Memcached rate-limit stores.

## Starter middleware behavior

`RateLimit` is configured as **route-level global middleware**, so it runs only after a route matches.

The starter application has policies for:

- web guests,
- authenticated web users,
- API requests, and
- sensitive routes such as login/register/forgot-password.

Configure them in `config/rate_limit.php`.

## Direct API

```php
use Bhitti\RateLimit\RateLimiter;

$result = RateLimiter::hit(
    'login:' . request()->ip(),
    10,
    60
);

if (!$result->allowed()) {
    return response()->json([
        'message' => 'Too many requests.',
        'retry_after' => $result->retryAfter(),
    ], 429);
}
```

Result methods:

```php
$result->allowed();
$result->limit();
$result->attempts();
$result->remaining();
$result->retryAfter();
$result->resetAt();
```

Clear a key:

```php
RateLimiter::clear($key);
```

Reset the active rate-limit driver:

```php
RateLimiter::reset();
```

## Redis

The Redis driver uses Lua for atomic counter/expiry behavior and the shared named Redis connection manager.

## Memcached

The Memcached driver uses the centralized Memcached connection manager shared with cache and sessions.

## Client IP behind proxies

IP-based rate limiting depends on `request()->ip()`. Configure only proxies you actually trust; Bhitti supports exact addresses and CIDR ranges and ignores forwarded client headers from untrusted direct connections.
