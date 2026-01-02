---
title: When and When Not to Use Try Catch in Laravel Controllers
author_username: dipesh79
date: 2026-01-02
tags: ["laravel", "php", "controllers", "exceptions"]
---

Avoid using try–catch blocks in controllers unless you have a clear and intentional reason. Laravel is designed to handle unexpected failures globally, and most controller logic does not need manual exception handling.

### When try–catch is not needed

In most cases, especially around authentication and validation, try–catch adds no value.

Example: Login

```php
auth()->attempt($credentials);
$user->createToken('authToken');
```

Why this does not need try–catch:

- Invalid credentials are a valid business outcome, not an exception
- Token creation failures should surface and be handled globally
- Laravel will log the error and return a proper 500 response

Using try–catch here often hides real bugs and clutters controllers with infrastructure concerns.

Example: Change password

```php
if (!Hash::check($oldPassword, $user->password)) {
    return validation error;
}

$user->update([...]);
```

Why try–catch is unnecessary:

- Invalid old passwords are business logic
- Validation already lives in FormRequest
- Database or hashing errors should not be swallowed

### When try–catch makes sense

Use try–catch only when you want to intentionally handle the failure.

Custom logging or alerting:

```php
try {
    $user->update(['password' => Hash::make($newPassword)]);
} catch (\\Throwable $e) {
    Log::critical('Password change failed', [
        'user_id' => $user->id,
        'exception' => $e,
    ]);

    return Response::error('Unable to change password right now.');
}
```

Translating technical errors into business responses:

```php
try {
    $user->createToken('authToken');
} catch (\\Throwable $e) {
    return Response::custom([], 503, 'Authentication service temporarily unavailable');
}
```

Handling failure around a transaction:

```php
try {
    DB::transaction(function () use ($user) {
        $user->tokens()->delete();
        $user->update(['last_login_at' => now()]);
        $user->createToken('authToken');
    });
} catch (\\Throwable $e) {
    return Response::error('Login failed. Please try again.');
}
```

### Rule of thumb

- Do not catch exceptions you cannot meaningfully handle
- Business rules are not exceptions
- Let Laravel handle the unexpected

Controllers should handle request flow, not hide failures. Use try–catch only when you need intentional control.
  