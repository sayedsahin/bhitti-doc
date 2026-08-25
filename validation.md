---
layout: default
title: Validation
---

# Validation

Create a validator from an input array:

```php
use Bhitti\Validation\Validator;

$validator = Validator::make(request()->all())
    ->required(['name', 'email'])
    ->string(['name', 'email'])
    ->email('email')
    ->max('name', 100);
```

## Checking validation

```php
if ($validator->fails()) {
    return response()->json([
        'errors' => $validator->errors(),
    ], 422);
}

$data = $validator->validated();
```

## Available rules

```php
->nullable('phone')
->required(['name', 'email'])
->string('name')
->int('age')
->bool('active')
->email('email')
->min('password', 8)
->max('name', 100)
->between('name', 8, 100)
->in('status', ['active', 'inactive'])
->confirmed('password')
->sometimes('company_name', $callback)
->custom($callback)
->bail()
```

`bail()` enables fail-fast validation: the first recorded failure throws `ValidationException`. Without `bail()`, inspect `fails()`/`errors()` after applying rules.

## Validation exceptions

`validated()` also throws `ValidationException` when validation has failed; otherwise it returns only fields that participated in validation rules.
