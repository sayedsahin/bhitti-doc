---
layout: default
title: Installation
---

# Installation

## Requirements

Bhitti requires PHP **8.3+**, Composer, PDO, Mbstring, and the PDO extension for your chosen SQL database.

Install optional extensions only when you use their drivers: PhpRedis, Memcached, or APCu.

## Get the application and framework

The current Bhitti application uses the framework as a sibling Composer path repository. Keep this layout:

```text
projects/
  bhitti/
  bhitti-framework/
```

Example:

```bash
git clone https://github.com/sayedsahin/bhitti.git
git clone https://github.com/sayedsahin/bhitti-framework.git
cd bhitti
composer install
```

The application `composer.json` resolves `sayedsahin/bhitti-framework` from `../bhitti-framework` and symlinks it during development.

Create the environment file:

```bash
cp .env.example .env
```

Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

## Configure the application

At minimum, set the application URL and database credentials:

```dotenv
APP_DEBUG=true
BASE_URL=http://127.0.0.1:8000
APP_TIMEZONE=UTC

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_NAME=bhitti
DB_USERNAME=root
DB_PASSWORD=
```

Run migrations:

```bash
php run migrate
```

Optionally seed the database:

```bash
php run db:seed
```

## Local development server

```bash
php -S 127.0.0.1:8000 -t public
```

The built-in PHP server is for local development. In production, point the web server document root at `public/` and run PHP through your chosen PHP-FPM deployment.

## Production caches

Before production traffic, generate configuration and route caches:

```bash
php run config:cache
php run route:cache
```

Generated files are stored under `storage/cache/`.
