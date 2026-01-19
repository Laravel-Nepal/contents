---
title: Type-Safe Config Access for static analysis in Laravel
author_username: achyut
date: 2026-01-19
tags: ["laravel", "config", "phpstan", "static-analysis"]
---

When we read values using `config('key')`, Laravel returns a `mixed` type. This is flexible, but it causes friction when you rely on static analysis tools like PHPStan or Psalm.

A `mixed` return means the analyzer cannot be sure what type you are working with, even when you clearly know it should be a string or an integer.

This usually shows up as unnecessary warnings, extra casts, or ignored errors.

Laravel provides a cleaner solution.

## The problem with `config()`

```php
$driver = config('cache.default');
```

From a static analysis perspective, `$driver` is `mixed`. That forces you to either cast manually or suppress the error.


## Type-safe config access

Laravel offers typed config accessors that return explicit types.

```php
use Illuminate\Support\Facades\Config;

$driver = Config::string('cache.default');
```

Or the helper instance:

```php
$driver = config()->string('cache.default');
```

Now, `$driver` is known to be a `string`, and static analysis tools can verify your code without false positives.

## Supported typed accessors

Laravel provides several typed helpers:

```php
config()->string('app.name');
config()->integer('queue.retry_after');
config()->float('services.tax.rate');
config()->boolean('app.debug');
config()->array('mail.mailers');
config()->collection('filesystems.disks');
```

Each method returns the expected type, making your code safer and cleaner.

## Benefits

In short, this will help you detect type-related issues early, reduce boilerplate casts, and improve code readability.

Embracing type-safe config access in Laravel enhances your development experience, especially when combined with static analysis tools. Give it a try in your next project!