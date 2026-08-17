---
layout: default
title: Service Container
---

# Service Container

Bhitti's container provides constructor autowiring, explicit bindings, singletons, instances, and runtime constructor parameters without a service-provider hierarchy.

## Resolve a class

```php
$service = $container->make(ReportService::class);
```

Concrete constructor dependencies are resolved recursively.

## Bind an abstraction

```php
$container->bind(
    MailerInterface::class,
    SmtpMailer::class
);
```

## Singleton

```php
$container->singleton(
    Metrics::class,
    Metrics::class
);
```

## Existing instance

```php
$container->instance(
    ClockInterface::class,
    $clock
);
```

## Runtime parameters

Middleware and other runtime-created services can receive explicit constructor values through `makeWith()`:

```php
$middleware = $container->makeWith(
    RoleMiddleware::class,
    ['admin']
);
```

Named parameters are also supported where they match constructor parameter names.

## Container configuration

Application-level singleton registrations live in `config/container.php`.

## Resolution behavior

Bhitti caches reflection metadata and detects circular dependency chains. Interfaces/abstract types must be bound unless a nullable/default constructor parameter lets the container omit them.

Union/intersection constructor types are intentionally not treated as automatic class dependencies; bind or provide those values explicitly.
