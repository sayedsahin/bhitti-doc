---
layout: default
title: Upgrade from v0.1.0
---

# Upgrade from v0.1.0

This page summarizes developer-visible changes made after the framework's `v0.1.0` baseline. The current code is authoritative when an older example conflicts with this guide.

Baseline alignment used for this documentation: framework tag `v0.1.0` points to commit `0efc13e` from 2026-08-07 09:42:19 UTC; the Bhitti application baseline is the matching `change package name` commit `27a7807` from 2026-08-07 09:43:37 UTC. Changes after those points were reviewed against the current source.

## Framework package boundary

The framework is now maintained as the `sayedsahin/bhitti-framework` Composer **library** while application-specific controllers, middleware, supports, helpers, configuration, migrations, seeders, and views remain in the Bhitti application.

## CLI entry point

Use:

```bash
php run ...
```

The command registry was reorganized and current framework commands include migration create/alter/run/rollback/status, seed creation/execution, and config/route/cache commands.

## Views

Views now live under:

```text
resources/views/
```

and use:

```text
*.view.php
```

`view()` returns the rendered string. Layouts and named sections are supported through the view object.

## Middleware/request flow

Global middleware is split into:

```text
middleware.kernel.*
middleware.route.*
```

Kernel middleware runs before routing and must remain stateless. Route middleware runs only after a match; web session configuration moved to the matched-route path. The old `SessionStart` application middleware is no longer part of the starter flow.

## Session changes

Remote sessions gained read/write access modes and stronger concurrent-write locking. Redis uses atomic token-aware Lua refresh/release, while Memcached uses atomic acquisition and CAS ownership refresh before writes.

Redis/Memcached custom PHP session handlers now use object-based session handler interfaces so the current code avoids PHP 8.4's deprecated multi-callback `session_set_save_handler()` form.

## Redis and Memcached connection management

Redis now uses named connection profiles and a shared request-local `RedisConnectionManager`. Memcached connection construction was likewise centralized so cache, session, and rate limiting share persistent ID/server/options setup.

## Cache

File cache expiration handling was hardened for concurrent refresh/delete races and small probabilistic cleanup. Redis flush is prefix-safe. Memcached cache uses shared connection management.

## Query Builder

Normal builder APIs now validate identifiers, aliases, operators, boolean connectors, join types, and order expressions. `selectRaw()` was added for explicit SQL expressions. Negative limits are rejected. Raw APIs remain developer-controlled escape hatches.

## Migrations

Migration command generation was reorganized into `migrate:create` and `migrate:alter`. Migration file checksum tracking was removed; rollback no longer needs `--allow-modified`. The repository tracks migration name, batch, and execution time.

## Seeders

Database seeding is now separate from migrations:

```bash
php run create:seeder users
php run db:seed
php run db:seed --filename=users
```

Seeders are closures and the ordered registry is `database/seeders/database.seeder.php`.

## Trusted proxies

Trusted proxies now support exact IPv4/IPv6 addresses and CIDR ranges. Forwarded host/scheme/client-IP data is considered only when the direct proxy is trusted.

## Redirects

`redirect()->to()` is local-only. Use `away()` for intentional external redirects.

## Starter application changes

The starter application moved to the new view structure, uses POST logout, removed the old session-start middleware, added the seeder registry/files, separated kernel/route middleware configuration, and added a composite unique constraint for user/role assignments.
