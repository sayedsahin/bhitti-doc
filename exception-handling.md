---
layout: default
title: Exception Handling
---

# Exception Handling

Bhitti installs a framework exception handler around application execution. Development and production output differ according to `APP_DEBUG`.

## Development

```dotenv
APP_DEBUG=true
```

Use development mode locally when you need detailed exception information.

## Production

```dotenv
APP_DEBUG=false
```

Do not expose stack traces or sensitive configuration to end users.

## Database exceptions

PDO is configured to throw database errors. Let `PDOException` describe database/runtime SQL failures rather than wrapping every execute call in a new generic exception.

Query Builder misuse is validated before SQL reaches PDO where needed—for example invalid identifiers/operators, negative limits, empty writes, and `UPDATE`/`DELETE` without a `WHERE` condition.

## Boot errors

Application boot configuration should fail visibly rather than silently swallowing exceptions. The current starter `AuthResolver` registration does not hide resolver registration errors.
