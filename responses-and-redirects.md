---
layout: default
title: Responses and Redirects
---

# Responses and Redirects

Use the `response()` helper to build HTTP responses.

## JSON

```php
return response()->json([
    'status' => 'success',
    'data' => $data,
], 200);
```

## HTML

```php
return response()->html('<h1>Hello</h1>', 200);
```

## Status and headers

```php
return response()
    ->status(201)
    ->header('X-App-Version', '1.0')
    ->json(['message' => 'Created'], 201);
```

Multiple headers can be added with `headers()`.

## Local redirects

Normal redirects are intentionally local:

```php
return response()->redirect()->to('/dashboard');
```

`to()` rejects absolute and scheme-relative URLs (`http://`, `https://`, `//`). This makes user-provided redirect targets safer by default.

## External redirects

Use `away()` explicitly for an external destination:

```php
return response()
    ->redirect()
    ->away('https://example.com');
```

## Redirect back

```php
return response()
    ->redirect()
    ->back('/');
```

Bhitti uses the referer only when it belongs to the same host as `BASE_URL`; otherwise it uses the fallback.

## Flash data with a redirect

```php
return response()
    ->redirect()
    ->with([
        'success' => 'Profile updated.',
    ])
    ->to('/profile');
```

`with()` stores the array in the current session under the `flash` key. The application-level `flash()` helper reads and consumes flash messages.
