---
layout: default
title: Twig View Template
---

# Twig View Template

Bhitti supports [Twig](https://twig.symfony.com) as an optional template engine.

Twig is not required by the framework. Applications that want to use it install it separately:

```bash
composer require twig/twig
```

Native `response()->view()` rendering remains available whether Twig is installed or not.

## Render a Twig template


```php
return response()->twig('welcome', [
    'title' => 'Bhitti',
]);
```

When the view name does not end in `.twig`, Bhitti treats it as dot notation and uses `.html.twig` by default.

```php
response()->twig('welcome');
```

loads:

```text
resources/views/welcome.html.twig
```

A nested view:

```php
response()->twig('admin.users.index');
```

loads:

```text
resources/views/admin/users/index.html.twig
```

## Explicit Twig filenames

If the view name already ends in `.twig`, it is passed to Twig as the template filename.

```php
return response()->twig('reports/users.csv.twig', [
    'users' => $users,
]);
```

Other examples:

```php
response()->twig('feeds/rss.xml.twig', $data);
response()->twig('emails/welcome.txt.twig', $data);
response()->twig('pages/home.html.twig', $data);
```

This allows Twig to be used for HTML as well as other text-based formats without forcing `.html.twig` on every template.

## Response status

`response()->twig()` defaults to HTTP status `200`.

Pass a non-200 status directly:

```php
return response()->twig(
    'errors.404',
    [],
    404
);
```

Or call `status()` after the renderer:

```php
return response()
    ->twig('errors.404')
    ->status(404);
```

> Call `status()` after `twig()`. Renderer methods default to `200` and will overwrite a status set before them.

## Basic Twig syntax

Escaped output:

```twig
<h1>{{ title }}</h1>
```

Conditionals:

```twig
{% if user %}
    <p>Hello {{ user.name }}</p>
{% endif %}
```

Loops:

```twig
<ul>
    {% for user in users %}
        <li>{{ user.name }}</li>
    {% endfor %}
</ul>
```

Twig handles its own template syntax and escaping behavior.

## CSRF token

Bhitti exposes the current CSRF token to Twig:

```twig
<input type="hidden" name="_csrf" value="{{ csrf_token() }}">
```

You may also use the provided CSRF field function:

```twig
<form method="post" action="/profile">
    {{ csrf_field() }}

    <button type="submit">Save</button>
</form>
```

`csrf_field()` returns the complete hidden input and is registered as safe HTML so Twig does not escape the generated tag.

## Compiled template cache

Twig compiles templates and stores the compiled result under:

```text
storage/cache/twig
```

The Twig environment is created lazily when `twig()` is first used.

Native `view()` rendering does not initialize Twig.

The Twig debug option follows:

```php
config('app.debug', false)
```

In production, keep application debug mode disabled so normal cached-template behavior is used.

## Native and Twig templates can coexist

Bhitti does not require a global template-engine switch.

Use the renderer explicitly:

```php
return response()->view('dashboard.index', $data);
```

for:

```text
resources/views/dashboard/index.view.php
```

and:

```php
return response()->twig('dashboard.index', $data);
```

for:

```text
resources/views/dashboard/index.html.twig
```

A project may therefore use native PHP templates for most pages and Twig only where desired.

## Output formats and responses

`twig()` always returns the rendered template as a string.

The template filename does not automatically change the HTTP response type.

For HTML:

```php
return response()->twig('pages.home', $data);
```

For CSV:

```php
return response()
    ->twig('exports/users.csv.twig', $data)
    ->header('Content-Type', 'text/csv; charset=utf-8');
```

For XML:

```php
return response()
    ->twig('feeds/rss.xml.twig', $data)
    ->header('Content-Type', 'application/xml; charset=utf-8');
```

The application is responsible for choosing the appropriate response headers for non-HTML formats.

## When to use Twig

Twig is useful when a project prefers:

- a dedicated template syntax;
- automatic template escaping;
- Twig filters, functions and control structures;
- a familiar external PHP template ecosystem.

Twig remains opt-in. Applications that use only native Bhitti views do not need to install it.

[Twig Official Documentation](https://twig.symfony.com)
