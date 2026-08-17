---
layout: default
title: Performance
---

# Performance

Bhitti's performance approach is architectural: avoid work that the current request does not need, keep abstractions small, and reuse external connections where appropriate.

## Route first, session later

Kernel-level stateless middleware runs before routing. Session-aware route middleware runs only when a route is found. As a result, 404/405 traffic does not initialize a web session or route-level middleware stack.

## Lazy cache resolution

The cache driver is resolved on the first cache operation rather than during every application boot.

## Session concurrency

Redis/Memcached sessions separate read and write access so read-only session access can close quickly. Write paths use ownership-aware locks to reduce lost updates when multiple PHP-FPM requests share the same browser session.

## Shared Redis connections

Cache, session, and rate limiting can map to the same named Redis profile. The request-local manager reuses one Redis object and supports persistent PHP-FPM connections.

## Shared Memcached connection

Memcached connection creation/options/server normalization are centralized, and cache/session/rate limiting reuse the same request-local object.

## Production caches

Use:

```bash
php run config:cache
php run route:cache
```

This removes repeated configuration loading and route compilation from the production hot path.

## Query Builder

Values use prepared PDO bindings; SQL state is reset between terminal operations. Use normal builder methods where possible and raw SQL when the database can express an operation more efficiently than the small builder API.

## Benchmark the whole stack

For meaningful results, benchmark the deployed path rather than isolated PHP method calls:

```text
Web server / reverse proxy
  ↓
PHP-FPM + OPcache
  ↓
Bhitti
  ↓
Database / Redis / Memcached as used
```

Useful request profiles include a plain route, a rendered view, a 404, a session read, a session write, one DB query, and one cache hit. Track throughput plus p50/p95/p99 latency and resource usage.
