---
layout: default
title: Deployment
---

# Deployment

Bhitti is designed for normal PHP-FPM deployments while keeping broad PHP hosting compatibility. The deployment platform is an application-owner decision; Bhitti does not require a particular web server or cloud provider.

## Document root

Point the public web root at:

```text
/path/to/project/public
```

Do not expose `.env`, `config/`, `storage/`, or application source files as public web content.

## Production environment

```dotenv
APP_DEBUG=false
BASE_URL=https://example.com
SESSION_SECURE=true
```

Set database/cache/session credentials through the environment.

## Composer

The current application Composer configuration resolves the framework from the sibling path `../bhitti-framework`. Ensure that source is available to Composer during deployment, or adjust your Composer repository strategy for your release process.

Then install production dependencies and optimize autoloading:

```bash
composer install --no-dev --optimize-autoloader
```

## Database

Run migrations explicitly:

```bash
php run migrate --force
```

Run seeders only when the deployment requires the registered seed data:

```bash
php run db:seed
```

## Build caches

```bash
php run config:cache
php run route:cache
```

Generated files:

```text
storage/cache/config.cache.php
storage/cache/route.cache.php
```

Ensure the PHP process can write the required `storage/cache` directories.

## PHP-FPM and persistent services

Redis connections are lazy and can use persistent PHP-FPM sockets. Memcached uses a shared request-local manager and a configured persistent ID. PDO persistence is opt-in rather than forced.

Measure your own workload before changing pool sizes, database persistence, Redis profiles, or cache drivers.

## Reverse proxies/load balancers

When running behind trusted infrastructure, configure exact proxy IPs or CIDR ranges:

```dotenv
TRUSTED_PROXIES=10.0.0.0/8,172.16.0.0/12
```

Do not trust public/uncontrolled address ranges.
