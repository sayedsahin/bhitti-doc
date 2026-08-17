---
layout: default
title: Database Seeding
---

# Database Seeding

Bhitti seeders are plain PHP files that return a closure. There is no base seeder class, seeder tracking table, or hidden execution graph.

## Create a seeder

```bash
php run create:seeder users
```

This creates:

```text
database/seeders/users.seeder.php
```

Template:

```php
<?php

declare(strict_types=1);

return static function (): void {
    //
};
```

The command also registers the filename in `database/seeders/database.seeder.php` unless that filename already appears in the registry text.

## Seeder registry

The registry returns filenames directly, in execution order:

```php
<?php

return [
    'roles.seeder.php',
    'users.seeder.php',
];
```

You can temporarily disable a registered seeder by commenting it out:

```php
return [
    'roles.seeder.php',
    // 'users.seeder.php',
];
```

`create:seeder` preserves an existing commented entry; it does not uncomment or duplicate it.

## Write a seeder

```php
<?php

declare(strict_types=1);

return static function (): void {
    db()->table('roles')->updateOrInsert(
        ['name' => 'admin'],
        ['name' => 'admin']
    );
};
```

Prefer idempotent seeders when the same seeder may be run more than once.

## Run registered seeders

```bash
php run db:seed
```

Bhitti executes active entries in `database.seeder.php` from top to bottom.

## Run one seeder

```bash
php run db:seed --filename=users
```

or:

```bash
php run db:seed --filename=users.seeder.php
```

Direct execution does not require the seeder to be active in `database.seeder.php`.
