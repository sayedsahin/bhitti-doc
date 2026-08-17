---
layout: default
title: Cache
---

# Cache

Bhitti provides array, file, APCu, Redis, and Memcached cache drivers behind one lazy cache facade.

## Configure the driver

```dotenv
CACHE_DRIVER=file
CACHE_PREFIX=bhitti:cache:
```

For Redis:

```dotenv
CACHE_DRIVER=redis
CACHE_REDIS_CONNECTION=default
```

## Basic API

```php
use Bhitti\Cache\Cache;

Cache::put('settings', $settings, 300);
$settings = Cache::get('settings');
Cache::has('settings');
Cache::forget('settings');
Cache::flush();
```

The `cache()` helper exposes the current cache interface where useful.

TTL `0` means no expiry for drivers that support that behavior. Negative TTL values are rejected.

## Remember

```php
$users = Cache::remember('active-users', 60, function () {
    return db()
        ->table('users')
        ->where('active', 1)
        ->get();
});
```

`remember()` intentionally remains a simple cache-aside helper; it does not add locking/stampede control to every cache miss.

## File cache

The file driver uses a stable cache lock, rechecks expired entries before deleting them, and performs small probabilistic garbage collection. This avoids deleting a freshly refreshed entry during concurrent PHP-FPM requests while still cleaning stale cache files over time.

## Redis cache

Redis cache uses the named `RedisConnectionManager` connection and prefix-safe `SCAN` cleanup rather than a database-wide flush.

## Memcached cache

Memcached cache uses the shared `MemcachedConnectionManager`. `flush()` is implemented through a namespace/version key so application cache invalidation does not require flushing the entire Memcached server.

## Serialization

File and Redis cache values use PHP serialization so mixed application values can be cached. Treat cache storage as application-controlled infrastructure; do not allow untrusted parties to write arbitrary cache payloads.
