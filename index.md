---
layout: default
title: Bhitti PHP Framework
---

# Bhitti PHP Framework

Bhitti is a lightweight PHP framework designed around three priorities: **performance, simplicity, and efficiency**. Its request model is well suited to PHP-FPM: work is deferred until it is needed, route misses are rejected early, database and cache connections are reused within a request, and production caches remove repeated bootstrap work.

## Requirements

- PHP 8.3 or newer
- Composer
- PDO
- Mbstring
- A PDO driver for the database you use (`pdo_mysql`, `pdo_pgsql`, or `pdo_sqlite`)

Optional extensions:

- PhpRedis for Redis cache, sessions, and rate limiting
- Memcached for Memcached cache, sessions, and rate limiting
- APCu for APCu cache and rate limiting

## Start here

```bash
git clone https://github.com/sayedsahin/bhitti.git
git clone https://github.com/sayedsahin/bhitti-framework.git
cd bhitti
composer install
cp .env.example .env
```

Keep the two repositories as sibling directories because the current application Composer configuration uses `../bhitti-framework` as a path repository. Set `APP_DEBUG=true` and your database values in `.env`, then run:

```bash
php run migrate
php -S 127.0.0.1:8000 -t public
```

On Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

Open `http://127.0.0.1:8000`.

## Core request flow

```text
Request
  ↓
Kernel middleware (stateless)
  ↓
Route matching
  ├─ 404 / 405 → response
  └─ FOUND
       ↓
     Session configuration for web routes
       ↓
     Route-level global middleware
       ↓
     Route/controller middleware
       ↓
     Controller
       ↓
     Response
```

This split keeps session-dependent work out of requests that never match a route.

## Main components

| Area | What Bhitti provides |
|---|---|
| Routing | FastRoute-based routes, groups, parameters, invokable controllers, route/controller middleware |
| Views | Plain PHP `.view.php` files with layouts and sections |
| Database | PDO, named SQL connections, transactions, a small Query Builder |
| Migrations | MySQL, PostgreSQL, SQLite schema migrations with rollback/status commands |
| Seeders | Closure-based ordered seed files and direct single-seeder execution |
| Sessions | Native, Redis, Memcached, and null drivers with lazy read/write behavior |
| Cache | Array, file, APCu, Redis, and Memcached drivers |
| Rate limiting | File, APCu, Redis, and Memcached stores |
| Container | Lightweight constructor autowiring, bindings, singletons, and runtime parameters |
| Security | CSRF, trusted-proxy handling with CIDR support, local-only normal redirects, validation and bound SQL values |

Bhitti intentionally avoids a large Active Record layer, a complex event system, or a heavyweight middleware pipeline. Use the components you need and keep application behavior explicit.
