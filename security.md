---
layout: default
title: Security
---

# Security

Bhitti provides small security primitives, but application security still depends on correct configuration and application code.

## CSRF

The starter web route middleware includes `Csrf`. Add a token to state-changing web forms:

```php
<?= $this->csrfField() ?>
```

API routes do not use the web CSRF/session stack by default.

## SQL safety

Query Builder values are PDO-bound. Normal builder methods validate identifiers, operators, boolean connectors, join types, aliases, and order expressions.

Raw SQL APIs are explicit escape hatches:

```php
raw()
selectRaw()
whereRaw()
orWhereRaw()
```

Never build their SQL strings from untrusted input.

## Redirect safety

Use local redirects normally:

```php
response()->redirect()->to('/dashboard');
```

Use `away()` only when an external redirect is intentional. `to()` rejects absolute and scheme-relative URLs.

## Trusted proxies

Only add infrastructure you control to `TRUSTED_PROXIES`. Exact IPv4/IPv6 addresses and CIDR ranges are supported.

Bhitti reads forwarded host, scheme, and client IP information only when the direct `REMOTE_ADDR` is trusted. This prevents a direct client from spoofing `X-Forwarded-*` headers into trusted request metadata.

## Sessions

Use secure, HTTP-only cookies in production and choose an appropriate SameSite value. Redis/Memcached session locking protects concurrent writes to the same session.

## Passwords and tokens

Use PHP's password hashing APIs for passwords. The starter bearer-auth implementation stores hashed API tokens rather than raw bearer tokens.

## Production debug mode

Keep:

```dotenv
APP_DEBUG=false
```

in production.
