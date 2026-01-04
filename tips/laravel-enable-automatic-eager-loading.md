---
title: Enable Automatic Eager Loading in Laravel
slug: laravel-enable-automatic-eager-loading
author_username: dipesh79
date: 2026-01-04
tags: ["laravel", "eloquent", "performance"]
---

Laravel 12.0.8 introduced automatic eager loading, which provides a global solution to prevent the N+1 query problem.

You can enable it once in your application and avoid repeating `with()` calls everywhere.

```php
// App\\Providers\\AppServiceProvider.php
use Illuminate\\Database\\Eloquent\\Model;

public function boot(): void
{
    Model::automaticallyEagerLoadRelationships();
}
```

With this enabled, related models are eager loaded automatically, keeping queries efficient and controllers clean.