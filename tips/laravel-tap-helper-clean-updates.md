---
title: Use the tap Helper for Cleaner Updates in Laravel
slug: laravel-tap-helper-clean-updates
author_username: dipesh79
date: 2026-03-019
tags: ["laravel", "php", "helpers"]
---

Laravel’s `tap()` helper lets you perform actions on a value while still returning the original instance. This is useful when you want to update a model and immediately return it in a clean and readable way.

Instead of separating update and return logic, `tap()` keeps everything concise.

### Example

```php
return tap($user)->update([
    'name' => $data['name'],
    'email' => $data['email'],
]);
// Returns User model
```

The `update()` method normally returns a boolean, but when used with `tap()`, the original `$user` instance is returned instead.

Without `tap()`, you would typically write:

```php
$user->update([
    'name' => $data['name'],
    'email' => $data['email'],
]); // returns bool

return $user; // returns User model
```

### Why use tap()

- Keeps code concise and expressive
- Avoids temporary variables or extra lines
- Makes intent clear when modifying and returning the same object

It’s a small helper, but it can make your service and controller code cleaner and easier to read.",