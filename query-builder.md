---
layout: default
title: Query Builder
---

# Query Builder

Bhitti's Query Builder keeps SQL construction small and explicit while binding values through PDO.

## Select

```php
$users = db()
    ->table('users')
    ->select('id', 'name', 'email')
    ->where('status', 'active')
    ->order('created_at DESC')
    ->limit(20)
    ->get();
```

Qualified identifiers and aliases are supported:

```php
$rows = db()
    ->table('users AS u')
    ->select('u.id', 'u.name')
    ->get();
```

## Raw select expressions

Normal `select()` accepts safe identifier syntax. Use `selectRaw()` for developer-controlled SQL expressions:

```php
$total = db()
    ->table('users')
    ->selectRaw('COUNT(id) AS total')
    ->first();
```

Do not pass user input to `selectRaw()`.

## Conditions

```php
$user = db()
    ->table('users')
    ->where('email', $email)
    ->first();
```

```php
$users = db()
    ->table('users')
    ->where('status', 'active')
    ->orWhere('role', 'admin')
    ->whereNull('deleted_at')
    ->whereNotNull('email')
    ->like('name', '%John%')
    ->get();
```

Normal operators are whitelisted, including comparison operators, `LIKE`/`NOT LIKE`, and PostgreSQL `ILIKE`/`NOT ILIKE`.

For a deliberate SQL fragment, use `whereRaw()`/`orWhereRaw()` and keep the SQL developer-controlled.

## Joins

```php
$users = db()
    ->table('users')
    ->leftJoin(
        'profiles',
        'profiles.user_id',
        '=',
        'users.id'
    )
    ->select('users.id', 'users.name', 'profiles.bio')
    ->get();
```

Normal join types are limited to `INNER`, `LEFT`, and `RIGHT`.

## Read methods

```php
->get();
->first();
->find(5);
->exists();
->count();
->pluck('email');
->value('email');
```

## Insert

```php
$id = db()
    ->table('users')
    ->insert([
        'name' => 'Rahim',
        'email' => 'rahim@example.com',
    ], true);
```

The second argument requests the inserted ID.

## Update

```php
db()
    ->table('users')
    ->where('id', 5)
    ->update(['status' => 'active']);
```

## Delete

```php
db()
    ->table('users')
    ->where('id', 5)
    ->delete();
```

Builder-generated `UPDATE` and `DELETE` operations require a `WHERE` condition.

## Update or insert

```php
db()
    ->table('settings')
    ->updateOrInsert(
        ['key' => 'theme'],
        ['value' => 'dark']
    );
```

Bhitti generates a native upsert for MySQL, PostgreSQL, and SQLite. The match columns must correspond to an appropriate unique/primary constraint.

## Raw SQL

```php
$users = db()
    ->raw(
        'SELECT * FROM users WHERE status = ?',
        ['active']
    )
    ->get();
```

```php
db()
    ->raw(
        'UPDATE users SET status = ? WHERE id = ?',
        ['active', 5]
    )
    ->execute();
```

`raw()`, `selectRaw()`, and `whereRaw()` are explicit escape hatches. Their SQL text must remain developer-controlled.

## Identifier safety

Normal builder methods validate table names, identifiers, aliases, order expressions, operators, booleans, and join types before SQL is generated. Values remain PDO-bound.

`limit()` rejects negative limits/offsets.

## Inspect generated SQL

```php
$sql = db()
    ->table('users')
    ->where('active', 1)
    ->toSql();
```

Bhitti does not currently provide `groupBy()` in the Query Builder; use a raw query when grouping is required.
