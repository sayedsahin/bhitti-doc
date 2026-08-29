---
layout: default
title: Native View Template
---

# Native View Template

Bhitti includes a native PHP view system with no external template dependency.

Native templates are stored under:

```text
resources/views/
```

and use the `.view.php` extension.

## Render a view

Render native views through the response object:

```php
return response()->view('welcome', [
    'title' => 'Bhitti',
]);
```

This loads:

```text
resources/views/welcome.view.php
```

`response()->view()` renders the template and returns the response object.

You may return that string directly from a controller:

```php
public function index(): string
{
    return response()->view('welcome', [
        'title' => 'Bhitti',
    ]);
}
```

## Response status

`response()->view()` defaults to HTTP status `200`.

Pass a non-200 status directly:

```php
return response()->view(
    'errors.404',
    [],
    404
);
```

Or call `status()` after the renderer:

```php
return response()
    ->view('errors.404')
    ->status(404);
```

> Call `status()` after `view()`. Renderer methods default to `200` and will overwrite a status set before them.

## Dot notation

Dot notation maps to directories.

```php
return response()->view('auth.login');
```

loads:

```text
resources/views/auth/login.view.php
```

For deeper directories:

```php
return response()->view('admin.users.index');
```

loads:

```text
resources/views/admin/users/index.view.php
```

## Passing data

Values passed in the data array are available as normal PHP variables inside the template.

Controller:

```php
return response()->view('users.profile', [
    'user' => $user,
    'title' => 'Profile',
]);
```

Template:

```php
<h1><?= $this->e($title) ?></h1>
<p><?= $this->e($user->name) ?></p>
```

## Escaping output

Use `$this->e()` for dynamic output inside a native view:

```php
<h1><?= $this->e($title) ?></h1>
```

The global `e()` helper is also available:

```php
<h1><?= e($title) ?></h1>
```

Both escape HTML special characters with UTF-8 encoding.

Only render unescaped HTML when the value is already trusted by your application.

## Native PHP syntax

Bhitti native templates are regular PHP files. There is no additional template language to learn.

```php
<?php if ($user): ?>
    <h1>Hello, <?= $this->e($user->name) ?></h1>
<?php endif; ?>
```

Loops use normal PHP syntax:

```php
<ul>
    <?php foreach ($users as $user): ?>
        <li><?= $this->e($user->name) ?></li>
    <?php endforeach; ?>
</ul>
```

## Layouts and sections

A child view may select a layout and define one or more sections.

```php
<?php $this->layout('layout.main'); ?>

<?php $this->start('content'); ?>
    <h1><?= $this->e($title) ?></h1>
<?php $this->end(); ?>
```

The layout can render the section:

```php
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <title><?= $this->e($title ?? 'Bhitti') ?></title>
</head>
<body>
    <main>
        <?= $this->section('content') ?>
    </main>
</body>
</html>
```

The layout above is stored at:

```text
resources/views/layout/main.view.php
```

A default value may be provided when reading a section:

```php
<?= $this->section('sidebar', '<p>No sidebar</p>') ?>
```

## Render another native view

Inside a native template, `$this->view()` may be used to render another view directly:

```php
<?php $this->view('partials.nav', [
    'user' => $user,
]); ?>
```

This is useful for small partial templates.

## CSRF field

For forms protected by the CSRF middleware, use:

```php
<form method="post" action="/profile">
    <?= $this->csrfField() ?>

    <button type="submit">Save</button>
</form>
```

This renders a hidden `_csrf` input using the current session CSRF token.

## When to use native views

Native views are the default choice when you want:

- no external template dependency;
- direct PHP syntax;
- minimal rendering overhead;
- simple debugging;
- layouts and sections without another template language.

For projects that prefer Twig syntax, Bhitti also provides optional Twig rendering through the `twig()` helper.
