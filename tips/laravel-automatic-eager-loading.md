---
title: Kill the N+1 Problem with Automatic Eager Loading in Laravel
slug: laravel-automatic-eager-loading
author_username: dipesh79
date: 2026-01-03
tags: [\"laravel\", \"eloquent\", \"performance\", \"database\"]
---

By default, Laravel uses lazy loading for relationships. This can silently introduce the N+1 query problem when accessing nested relationships in loops.

Example:

```php
$users = User::all();

foreach ($users as $user) {
    echo $user->district->state->country->name;
}
```

This can easily result in hundreds of database queries.

### Automatic eager loading (Laravel 12.0.8+)

Laravel now allows you to globally enable automatic eager loading for model relationships. This removes the need to repeatedly call `with()` throughout your codebase.

Enable it once in your application:

```php
// AppServiceProvider.php
use Illuminate\\Database\\Eloquent\\Model;

public function boot(): void
{
    Model::automaticallyEagerLoadRelationships();
}
```

Now even a simple query like:

```php
User::all();
```

will automatically eager load related models and prevent N+1 queries, keeping controllers and services clean.

### Catch performance issues early

To make performance problems visible during development, disable lazy loading in non-production environments:

```php
Model::preventLazyLoading(!app()->isProduction());
```

This helps you detect unintended lazy loading before it reaches production.

### Why this matters

- No repetitive `with()` calls
- Cleaner controllers and services
- Fewer database queries by default
- Better application performance

A small configuration change that has a big impact on Eloquent performance.
  