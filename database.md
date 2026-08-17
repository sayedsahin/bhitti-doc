---
layout: default
title: Database
---

# Database

Bhitti uses PDO and supports named MySQL, PostgreSQL, and SQLite connections.

## Default connection

Configure `DB_CONNECTION` and the related credentials in `.env`:

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_NAME=bhitti
DB_USERNAME=root
DB_PASSWORD=
```

Use the default connection:

```php
$users = db()->table('users')->get();
```

Use a named connection:

```php
$users = db('sqlite')
    ->table('users')
    ->get();
```

Each `db()` call creates fresh Query Builder state while Bhitti reuses the underlying database connection within the request.

## Transactions

The database layer exposes transactions through the configured connection:

```php
$db = db();

$db->transaction(function () {
    db()->table('orders')->insert([...]);
    db()->table('audit_logs')->insert([...]);
});
```

The `DB` object returned by `db()` also exposes `beginTransaction()`, `commit()`, `rollBack()`, and `inTransaction()` when manual control is required.

## Redis connections

Redis is configured under `database.redis.connections`. Cache, session, and rate limiting can all point at the same named profile:

```php
'cache.redis.connection'      => 'default'
'session.redis.connection'    => 'default'
'rate_limit.redis.connection' => 'default'
```

The framework's `RedisConnectionManager` lazily reuses a request-local Redis object. With persistent connections enabled, PHP-FPM workers can reuse the underlying Redis socket between requests.

Add another named Redis profile when a service needs an isolated endpoint or database.

## Memcached connection

Memcached connection setup is centralized under `database.memcached`. Cache, session, and rate-limit drivers share the same request-local Memcached object.

The connection manager handles persistent IDs, server normalization, binary protocol, connect timeout, and consistent hashing for multiple servers.
