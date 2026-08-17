---
layout: default
title: Migrations
---

# Migrations

Migrations are plain PHP files returning `up` and `down` closures. The migration system supports MySQL, PostgreSQL, and SQLite.

## Create a migration

```bash
php run migrate:create users
```

Create an alteration migration:

```bash
php run migrate:alter users
```

Both commands accept `--path=...` for a custom migration directory.

## Migration shape

```php
<?php

use Bhitti\Database\Migration\Schema;
use Bhitti\Database\Migration\Blueprint as Table;

return [
    'up' => static function (): void {
        Schema::create('users', static function (Table $table): void {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamps();
        });
    },

    'down' => static function (): void {
        Schema::dropIfExists('users');
    },
];
```

## Run migrations

```bash
php run migrate
```

Specify a named SQL connection:

```bash
php run migrate --connection=pgsql
```

## Rollback

Rollback the last batch:

```bash
php run migrate:rollback
```

Rollback a number of most recent migrations:

```bash
php run migrate:rollback --step=2
```

Bhitti no longer stores migration file checksums. Editing an already-run migration does not require `--allow-modified`; rollback executes the current `down` closure. In a deployed application, prefer a new migration for subsequent schema changes.

## Status

```bash
php run migrate:status
```

Statuses are `pending`, `ran`, or `missing`.

The migration repository stores:

```text
migration
batch
executed_at
```

## Production guard

When application debug mode is off, `migrate` and `migrate:rollback` require explicit confirmation with:

```bash
php run migrate --force
php run migrate:rollback --force
```

## Atomic execution

Bhitti uses migration locking to prevent concurrent migration runners. PostgreSQL and SQLite migration steps are wrapped transactionally where supported; MySQL DDL is not treated as transaction-safe by the migrator.
