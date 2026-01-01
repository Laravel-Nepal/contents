---
title: When to Use DB Transactions in Laravel
slug: laravel-db-transactions
author_username: dipesh79
date: 2026-01-01
tags: ["laravel", "php", "database", "transactions"]
---

Database transactions are useful in Laravel, but they are not needed for every operation that touches the database. Using them unnecessarily can make your code more complicated without any real benefit.

### When transactions are not needed

For example, during a login or simple user actions:

```php
auth()->attempt($credentials);
$user->createToken('authToken');
```

These operations are independent. If creating the token fails, it does not cause any major issues with your data. Laravel will handle exceptions globally, so wrapping this in a transaction would only add complexity.

### When transactions make sense

Transactions should be used when multiple database operations depend on each other and must succeed or fail together. For example, if you need to revoke old tokens, update audit information, and create a new token:

```php
DB::transaction(function () use ($user) {
    $user->tokens()->delete();
    $user->update(['last_login_at' => now()]);
    $token = $user->createToken('authToken')->plainTextToken;
});
```

In this case, a transaction ensures that either all operations succeed or none do, preventing your system from ending up in an inconsistent state.

### Rule of thumb

- If partial success is acceptable, you don’t need a transaction.
- If partial success could break your data consistency, use a transaction.

Using transactions only when necessary keeps your code simple, reliable, and easy to maintain.

