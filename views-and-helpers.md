---
layout: default
title: Views and Helpers
---

# Views and Helpers

Bhitti views are plain PHP templates stored under `resources/views/` with the `.view.php` extension.

## Render a view

```php
return view('welcome', [
    'title' => 'Bhitti',
]);
```

Dot notation maps to directories:

```php
view('auth.login');
```

loads:

```text
resources/views/auth/login.view.php
```

`view()` returns the rendered string rather than echoing it directly.

## Escaping output

Use `e()` for dynamic text:

```php
<h1><?= $this->e($title) ?></h1>
```

Outside a view object you may use:

```php
// Supports/Message.php

<?= e($title) ?>
```

Only output unescaped HTML when the value is already trusted by your application.

## Layouts and sections

A child view can select a layout and define sections:

```php
<?php $this->layout('layout.main'); ?>

<?php $this->start('content'); ?>
    <h1><?= $this->e($title) ?></h1>
<?php $this->end(); ?>
```

Then `resources/views/layout/main.view.php` can render the section:

```php
<!doctype html>
<html>
<body>
    <main>
        <?= $this->section('content') ?>
    </main>
</body>
</html>
```

## CSRF field

For web forms protected by the CSRF middleware:

```php
<form method="post" action="/profile">
    <?= $this->csrfField() ?>
    <button type="submit">Save</button>
</form>
```

## Framework helpers

Common framework helpers include:

```php
cache();
db();
session();
env();
config();
request();
response();
view();
e();
csrf_token();
is_ajax();
is_api_request();
pr();
dd();
```

## Application helpers

The starter application defines application-specific helpers separately in `app/Helpers/common.php`, including:

```php
auth();
role();
roles();
flash();
```

Keeping application helpers outside the framework package preserves the boundary between reusable framework behavior and project-specific behavior.
