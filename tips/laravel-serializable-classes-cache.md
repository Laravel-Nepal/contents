---
title: Safer Cache with serializable_classes in Laravel 13
slug: laravel-serializable-classes-cache
author_username: dipesh79
date: 2026-05-04
tags: ["laravel", "cache", "security"]
---

Laravel 13 introduced a new cache security feature called `serializable_classes` to make caching safer.

### What it does

```php
'serializable_classes' => false,
```

- Prevents Laravel from unserializing arbitrary PHP objects from cache
- Protects against deserialization attacks if your app key is compromised
- Returns `__PHP_Incomplete_Class` for disallowed objects

### What breaks

If you were caching objects like:

- Eloquent models
- Collections or paginators
- Custom classes

They will no longer unserialize unless explicitly allowed.

### How to fix

Allow specific classes:

```php
// config/cache.php
'serializable_classes' => [
    App\\Data\\CachedSomething::class,
],
```

Only whitelist simple and safe classes.

Or avoid object caching entirely:

```php
$data->toArray();
$query->pluck('id');
```

### Best practice

Cache data, not objects.

- Safer
- Easier to maintain
- More scalable

This change encourages better architecture and more secure caching practices.