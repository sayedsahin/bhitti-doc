---
layout: default
title: Sessions and Cookies
---

# Sessions and Cookies

Bhitti supports `native`, `redis`, `memcached`, and `null` session drivers. Web sessions are configured only after a route is found; API routes do not configure PHP sessions by default.

## Configuration

```dotenv
SESSION_ENABLED=true
SESSION_DRIVER=native
SESSION_NAME=BHITTISESSID
SESSION_LIFETIME=7200
SESSION_SECURE=true
SESSION_HTTP_ONLY=true
SESSION_SAMESITE=Lax
```

Remote sessions also use:

```dotenv
SESSION_PREFIX=bhitti:session:
SESSION_LOCK=true
SESSION_LOCK_TTL=10
SESSION_LOCK_WAIT=2
SESSION_LOCK_SLEEP=20000
```

Redis sessions can select a named Redis profile:

```dotenv
SESSION_REDIS_CONNECTION=default
```

When `SESSION_ENABLED=false`, Bhitti installs the null driver for matched web routes so session calls remain safe without persistent session state.

## Session API

```php
use Bhitti\Session\Session;

$value = Session::get('cart', []);
Session::set('cart', $cart);
Session::forget('cart');
Session::flush();
Session::regenerate();
Session::destroy();
Session::close();
```

The `session()` helper provides access to the configured session interface where preferred.

## Lazy read/write behavior

Remote session drivers distinguish read access from write access. Read-only access can load and close the PHP session quickly, avoiding a long exclusive session lock. Write access obtains a lock and verifies ownership before remote session state is changed.

This is important for concurrent PHP-FPM requests from the same browser session.

## Redis locking

Redis sessions use an atomic `SET ... NX PX` lock. Ownership refresh and release use Redis Lua scripts so the token check and TTL/delete operation are atomic.

## Memcached locking

Memcached sessions use `add()` for lock acquisition and CAS when refreshing an owned lock before writing. Release verifies the owner token before deleting the lock.

## PHP 8.4 session handler registration

Redis and Memcached sessions use an object handler implementing PHP's session handler interfaces and register it with the two-argument `session_set_save_handler($handler, true)` form. This avoids the deprecated multi-callback registration signature in PHP 8.4 while retaining session ID validation and lazy timestamp updates.

## Cookies

```php
use Bhitti\Session\Cookie;

Cookie::set('theme', 'dark', 3600);
$value = Cookie::get('theme');
Cookie::forget('theme');
```

Keep sensitive cookies secure and HTTP-only where applicable.
