---
title: Extend Cache TTL Using Cache touch in Laravel
slug: laravel-cache-touch
author_username: dipesh79
date: 2026-04-01
tags: ["laravel", "cache", "performance"]
---

Laravel 13 introduced a simple way to extend the lifetime of an existing cache key without retrieving or rewriting the data using `Cache::touch()`.

### Example

```php
use Illuminate\\Support\\Facades\\Cache;

Cache::touch('user_session', 3600);
```

This updates the expiration time of the cache key without modifying its value.

### Real use case

```php
use Illuminate\\Support\\Facades\\Cache;

if (Cache::has('user_'.$user->id)) {
    Cache::touch('user_'.$user->id, 3600);
}
```

This is useful when you want to keep frequently accessed data alive without re-fetching or re-storing it.

### Why use it

- Extends cache TTL without fetching the value
- Avoids unnecessary database or Redis calls
- Improves performance for frequently accessed data

It’s a small feature, but it helps keep your cache efficient and reduces load on your application.