---
layout: default
title: Troubleshooting
---

# Troubleshooting

## `php run` cannot load the framework

Run Composer first:

```bash
composer install
```

Confirm `vendor/autoload.php` exists and the application requires `sayedsahin/bhitti-framework`.

## Configuration changes are not visible

When using cached production configuration:

```bash
php run config:cache
```

Or clear caches:

```bash
php run cache:clear
```

## Route changes are not visible

Regenerate the route cache:

```bash
php run route:cache
```

## Session driver error

Check:

```dotenv
SESSION_ENABLED=true
SESSION_DRIVER=native
```

For Redis/Memcached, confirm the required PHP extension and service connection. If sessions are intentionally disabled, Bhitti uses the null session driver on matched web routes.

## Redis connection error

Verify the named profile in `config/database.php` and the service's connection name in `cache.php`, `session.php`, or `rate_limit.php`.

## Memcached connection error

Verify the Memcached PHP extension, host/port, and server configuration under `database.memcached`.

## Wrong client IP or scheme behind a proxy

Set only trusted proxy addresses/ranges:

```dotenv
TRUSTED_PROXIES=10.0.0.0/8
```

If `REMOTE_ADDR` is not trusted, Bhitti intentionally ignores forwarded headers.

## Migration repository mismatch after upgrading old development databases

Current migration tracking no longer requires a `checksum` column. If an older development database still has a non-null checksum column, recreate or update the migration repository before using the current migration logger.

## `db:seed` skips a file

Check `database/seeders/database.seeder.php`. Commented entries are intentionally skipped by full seeding. Run a file explicitly with:

```bash
php run db:seed --filename=users
```

## Redirect to an external URL fails

`redirect()->to()` is local-only. Use:

```php
response()->redirect()->away('https://example.com');
```
