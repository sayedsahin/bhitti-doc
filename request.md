---
layout: default
title: Request
---

# Request

Access the current request with:

```php
$request = request();
```

## Input

```php
$all = request()->all();
$email = request()->input('email');
$page = request()->query('page', 1);
$name = request()->post('name');
```

## JSON and raw body

```php
$data = request()->json();
$email = request()->json('email');
$body = request()->getRawBody();
```

## Files, cookies, headers

```php
$file = request()->file('avatar');
$theme = request()->cookie('theme', 'light');
$accept = request()->header('accept');
$token = request()->bearerToken();
```

## Request metadata

```php
$method = request()->method();
$isPost = request()->isMethod('POST');
$path = request()->path();
$url = request()->fullUrl();
$host = request()->host();
$ip = request()->ip();
$secure = request()->isSecure();
$api = request()->isApi();
```

## Trusted proxy behavior

Bhitti trusts `X-Forwarded-*` metadata only when `REMOTE_ADDR` matches an entry in `app.trusted_proxies`. Exact IPv4/IPv6 addresses and CIDR ranges are supported.

For `X-Forwarded-For`, Bhitti walks the proxy chain from the right and returns the first valid untrusted address. This keeps `request()->ip()` useful behind trusted proxy chains without accepting arbitrary forwarded headers from direct clients.
