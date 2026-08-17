---
layout: default
title: Configuration
---

# Configuration

Bhitti uses environment variables for deployment-specific values and plain PHP files under `config/` for application configuration.

## Environment values

Use `env()` inside configuration files:

```php
return [
    'debug' => (bool) env('APP_DEBUG', false),
    'url' => (string) env('BASE_URL', 'http://localhost'),
];
```

Read resolved configuration elsewhere with dot notation:

```php
config('app.debug');
config('database.default');
config('session.driver', 'native');
```

## Important environment settings

```dotenv
APP_NAME=Bhitti Framework
APP_DEBUG=false
BASE_URL=https://example.com
APP_TIMEZONE=UTC

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_NAME=bhitti
DB_USERNAME=app
DB_PASSWORD=secret

SESSION_ENABLED=true
SESSION_DRIVER=native
CACHE_DRIVER=file
RATE_LIMIT_STORE=file
```

## Trusted proxies

Trusted proxies may be exact IPv4/IPv6 addresses or CIDR ranges:

```dotenv
TRUSTED_PROXIES=127.0.0.1,10.0.0.0/8,172.16.0.0/12
```

Leave the value unset/empty when the application is not behind a trusted reverse proxy.

## Configuration cache

Generate the cache:

```bash
php run config:cache
```

Bhitti writes:

```text
storage/cache/config.cache.php
```

`routes.php` and `commands.php` are excluded because they contain runtime definitions. Middleware configuration is cacheable.

When `APP_DEBUG=true`, Bhitti uses live configuration and removes a stale configuration cache. In production, cache configuration as part of deployment.

Clear generated/application caches with:

```bash
php run cache:clear
```
