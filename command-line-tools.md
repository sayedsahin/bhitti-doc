---
layout: default
title: Command-line Tools
---

# Command-line Tools

Bhitti's CLI entry point is the root `run` script:

```bash
php run
```

## Migrations

Create a table migration:

```bash
php run migrate:create users
```

Create an alteration migration:

```bash
php run migrate:alter users
```

Custom path:

```bash
php run migrate:create users --path=database/custom-migrations
```

Run pending migrations:

```bash
php run migrate
```

Named connection:

```bash
php run migrate --connection=pgsql
```

Rollback the latest batch:

```bash
php run migrate:rollback
```

Rollback a number of migrations:

```bash
php run migrate:rollback --step=2
```

Status:

```bash
php run migrate:status
```

When `APP_DEBUG=false`, migration and rollback commands require `--force`.

## Seeders

Create and register a seeder:

```bash
php run create:seeder users
```

Run active registered seeders:

```bash
php run db:seed
```

Run one file:

```bash
php run db:seed --filename=users
```

## Caches

Configuration cache:

```bash
php run config:cache
```

Route cache:

```bash
php run route:cache
```

Rebuild the route cache after changing route definitions or controller class/method `#[Middleware]` attributes, because controller middleware metadata is collected when routes are registered.

Clear application/generated caches:

```bash
php run cache:clear
```

`cache:clear` clears the active cache store and known file-backed/generated cache locations. APCu is process-local, so the command warns when clearing CLI APCu does not affect a separate PHP-FPM APCu process.

## Application commands

Application-specific command definitions live in `config/commands.php`; framework commands are registered separately by the framework's `FrameworkCommands` registry.
