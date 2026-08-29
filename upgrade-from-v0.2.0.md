---

layout: default
title: Upgrade from v0.2.0
--------------------------

# Upgrade from v0.2.0

Bhitti `v0.3.0` introduces three developer-facing changes.

## 1. Native view rendering moved to the Response API

Previously:

```php
public function index(): string
{
    return view('users.index', [
        'users' => $users,
    ]);
}
```

Use:

```php
public function index(): Response
{
    return response()->view('users.index', [
        'users' => $users,
    ]);
}
```

Import the response class when using a return type:

```php
use Bhitti\Http\Response;
```

For a non-200 response:

```php
public function notFound(): Response
{
    return response()
        ->view('errors.404')
        ->status(404);
}
```

## 2. Optional Twig template support

`v0.3.0` adds official Twig support.

Install Twig in applications that want to use it:

```bash
composer require twig/twig
```

Render a Twig template:

```php
public function index(): Response
{
    return response()->twig('users.index', [
        'users' => $users,
    ]);
}
```

Without a `.twig` suffix, the default HTML template is resolved as:

```text
resources/views/users/index.html.twig
```

Explicit Twig filenames can also be used:

```php
return response()->twig('feeds/rss.xml.twig', $data);
```

For non-HTML output:

```php
return response()
    ->twig('feeds/rss.xml.twig', $data)
    ->header('Content-Type', 'application/xml');
```

Native PHP templates remain available:

```text
resources/views/*.view.php
```

## 3. Controller view return types

Controller methods that previously returned rendered view strings:

```php
public function index(): string
{
    return view('users.index');
}
```

should now return `Response`:

```php
public function index(): Response
{
    return response()->view('users.index');
}
```

The same applies to Twig:

```php
public function index(): Response
{
    return response()->twig('users.index');
}
```

## Upgrade checklist

Update:

```php
return view('example');
```

to:

```php
return response()->view('example');
```

Change the controller return type:

```php
string
```

to:

```php
Response
```

and add:

```php
use Bhitti\Http\Response;
```

When using Twig:

```bash
composer require twig/twig
```

Then:

```php
return response()->twig('example');
```

No other application changes are required for this release.
