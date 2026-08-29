---
layout: default
title: Helpers
---

# Helpers

Bhitti provides small global helper functions for common framework operations.

Framework helpers are loaded by the framework package. The starter application may define additional application-specific helpers separately in:

```text
app/Helpers/common.php
```

## `cache()`

Returns access to the configured cache system.

Store a value:

```php
cache()->put('settings.theme', 'dark', 300);
```

Read a value:

```php
$theme = cache()->get('settings.theme', 'light');
```

Check whether a key exists:

```php
if (cache()->has('settings.theme')) {
    // ...
}
```

Remove a key:

```php
cache()->forget('settings.theme');
```

Clear the configured cache:

```php
cache()->flush();
```

Cache a computed value:

```php
$user = cache()->remember(
    'user:' . $id,
    300,
    fn () => db()->table('users')->find($id)
);
```

A TTL of `0` means no explicit expiration. A negative TTL is rejected.

## `db()`

Returns a fresh Query Builder using the configured database service.

Basic query:

```php
$users = db()
    ->table('users')
    ->where('status', 'active')
    ->get();
```

Select specific columns:

```php
$users = db()
    ->table('users')
    ->select('id', 'name', 'email')
    ->get();
```

Use a named connection:

```php
$logs = db('reporting')
    ->table('logs')
    ->get();
```

Run a transaction:

```php
$userId = db()->transaction(function () use ($data) {
    return db()
        ->table('users')
        ->insert($data, true);
});
```

Each `db()` call returns a new Query Builder while reusing the configured `Database` service and its cached PDO connection.

## `session()`

Returns a small proxy to the active session driver.

Store a value:

```php
session()->set('theme', 'dark');
```

Read a value:

```php
$theme = session()->get('theme', 'light');
```

Remove a value:

```php
session()->forget('theme');
```

Clear session data:

```php
session()->flush();
```

Regenerate the session identifier:

```php
session()->regenerate();
```

Destroy the session:

```php
session()->destroy();
```

Close the session after session work is complete:

```php
session()->close();
```

Sessions are initialized for matching web routes. API requests are session-free by default.

## `env()`

Reads an environment value.

```php
$debug = env('APP_DEBUG', false);
```

A default may be provided:

```php
$host = env('DB_HOST', '127.0.0.1');
```

Common scalar strings are converted automatically:

```text
true   -> bool
false  -> bool
null   -> null
123    -> int
12.5   -> float
```

Application code should normally read application settings through `config()`. `env()` is most useful inside configuration files.

## `config()`

Reads or updates loaded configuration.

Read a value:

```php
$debug = config('app.debug', false);
```

Dot notation accesses nested configuration:

```php
$driver = config('database.default');
```

Return all configuration:

```php
$all = config();
```

Set several values at runtime:

```php
config([
    'app.debug' => true,
    'cache.driver' => 'array',
]);
```

A default may be a closure because missing values are resolved through the `value()` helper:

```php
$name = config('app.name', fn () => 'Bhitti');
```

## `value()`

Returns a normal value unchanged, or executes a closure and returns its result.

```php
$name = value('Bhitti');
```

returns:

```text
Bhitti
```

A closure:

```php
$name = value(fn () => 'Bhitti');
```

also returns:

```text
Bhitti
```

This helper is useful for lazy default values.

## `request()`

Returns the shared `Bhitti\Http\Request` instance from the service container.

Read request input:

```php
$email = request()->input('email');
```

Query string:

```php
$page = request()->query('page', 1);
```

POST data:

```php
$name = request()->post('name');
```

JSON:

```php
$data = request()->json();
$email = request()->json('email');
```

Header:

```php
$accept = request()->header('accept');
```

Bearer token:

```php
$token = request()->bearerToken();
```

Method and path:

```php
if (request()->isMethod('POST')) {
    // ...
}

$path = request()->path();
```

Client information:

```php
$ip = request()->ip();
$secure = request()->isSecure();
```

## `response()`

Creates a `Bhitti\Http\Response`.

Plain response:

```php
return response('Hello', 200);
```

HTML:

```php
return response()->html('<h1>Hello</h1>');
```

JSON:

```php
return response()->json([
    'status' => 'ok',
]);
```

Set a header:

```php
return response('Created', 201)
    ->header('X-App-Version', '1.0');
```

Set multiple headers:

```php
return response()
    ->json($data)
    ->headers([
        'Cache-Control' => 'no-store',
        'X-Request-ID' => $requestId,
    ]);
```

Redirect:

```php
return response()->redirect('/login');
```

External redirect:

```php
return response()
    ->redirect()
    ->away('https://example.com');
```

## Template rendering

Template rendering is part of the response API rather than a global helper.

Native PHP template:

```php
return response()->view('users.index', [
    'users' => $users,
]);
```

Twig template:

```php
return response()->twig('users.index', [
    'users' => $users,
]);
```

See **Native View Template** and **Twig View Template** for full template documentation.

## `e()`

Escapes a value for safe HTML output.

```php
echo e($user->name);
```

Example:

```php
<h1><?= e($title) ?></h1>
```

It converts the value to a string and escapes HTML special characters using UTF-8.

Inside native views, `$this->e()` calls the same helper.

## `csrf_token()`

Returns the current session CSRF token.

```php
$token = csrf_token();
```

If no token exists yet, Bhitti generates one and stores it in the session.

Manual form example:

```php
<input
    type="hidden"
    name="_csrf"
    value="<?= e(csrf_token()) ?>"
>
```

In a native view, the shorter form is:

```php
<?= $this->csrfField() ?>
```

In Twig:

```twig
{{ csrf_field() }}
```

## `is_ajax()`

Checks whether the current request contains:

```text
X-Requested-With: XMLHttpRequest
```

Example:

```php
if (is_ajax()) {
    return response()->json([
        'status' => 'ok',
    ]);
}
```

## `is_api_request()`

Returns whether the current request path belongs to the API namespace.

```php
if (is_api_request()) {
    // API-specific behavior
}
```

The request is considered an API request when the normalized path is `/api` or starts with `/api/`.

## `pr()`

Prints one or more values inside a `<pre>` block using `print_r()`.

```php
pr($user);
```

Several values:

```php
pr($user, $roles, $requestData);
```

`pr()` does not stop execution.

This is a development/debugging helper and should not be left in production response paths.

## `dd()`

Dumps one or more values with `var_dump()` and immediately stops execution.

```php
dd($user);
```

Several values:

```php
dd($user, $roles);
```

Because `dd()` terminates execution, it is intended only for development and debugging.

---

# Starter Application Helpers

The Bhitti starter application defines application-specific helpers in:

```text
app/Helpers/common.php
```

These helpers are not part of the reusable framework package.

## `auth()`

Returns the starter application's `App\Supports\Auth` service.

Check authentication:

```php
if (auth()->check()) {
    // authenticated
}
```

Current authenticated user ID:

```php
$userId = auth()->id();
```

Current user:

```php
$user = auth()->user();
```

Login:

```php
auth()->login($userId);
```

Logout:

```php
auth()->logout();
```

Check whether the current authentication came from remember-me:

```php
if (auth()->viaRemember()) {
    // ...
}
```

## `role()`

Checks whether the authenticated user has one role.

```php
if (role('admin')) {
    // ...
}
```

It returns a boolean.

## `roles()`

Checks whether the authenticated user has any role from the provided list.

```php
if (roles(['admin', 'editor'])) {
    // ...
}
```

It returns `true` when at least one role matches.

## `flash()`

Renders and consumes the current starter-application flash message.

```php
<?= flash() ?>
```

It may render success, error, warning, info or validation-error output.

After rendering, the flash message is removed from the session.

Keeping these application helpers outside the framework package preserves the boundary between reusable framework behavior and project-specific application behavior.
