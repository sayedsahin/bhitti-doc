---
layout: default
title: Directory Structure
---

# Directory Structure

A current Bhitti application is intentionally small:

```text
app/
  Controllers/
  Helpers/
  Middlewares/
  Models/
  Supports/
bootstrap/
  app.php
  services.php
config/
  app.php
  cache.php
  commands.php
  container.php
  database.php
  middleware.php
  rate_limit.php
  routes.php
  session.php
database/
  migrations/
  seeders/
public/
  index.php
resources/
  views/
storage/
  cache/
vendor/
.env
run
```

## `app/`

Application code lives here. Framework internals are provided by the `sayedsahin/bhitti-framework` Composer package rather than being copied into the application.

## `bootstrap/`

`bootstrap/app.php` defines `ROOT_PATH`, `APP_PATH`, and `STORAGE_PATH`, loads Composer, and returns the application instance. `bootstrap/services.php` contains application-level boot registrations such as the auth resolver.

## `config/`

Configuration is plain PHP. `routes.php` and `commands.php` are runtime definitions and are intentionally excluded from configuration caching.

## `database/`

- `migrations/` contains schema changes.
- `seeders/` contains closure-based seed files plus `database.seeder.php`, which defines the default seeding order.

## `resources/views/`

Views use the `.view.php` extension. Dot notation maps to subdirectories: `view('auth.login')` loads `resources/views/auth/login.view.php`.

## `public/`

This is the web document root. Requests enter through `public/index.php`.

## `storage/cache/`

Generated configuration/route cache files and file-backed cache/rate-limit data live here.

## `run`

The root `run` script is Bhitti's CLI entry point:

```bash
php run
```
