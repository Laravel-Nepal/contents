---
title: Detect Lazy Loading During Development in Laravel
slug: laravel-detect-lazy-loading
author_username: dipesh79
date: 2026-01-04
tags: ["laravel", "eloquent", "performance", "debugging"]
---

Lazy loading can silently introduce serious performance issues. During development, it’s better to fail fast and catch these problems early.

Laravel allows you to disable lazy loading in non-production environments so that issues are immediately visible.

Enable this in the `boot` method of your `AppServiceProvider`:

```php
use Illuminate\\Database\\Eloquent\\Model;

Model::preventLazyLoading(!app()->isProduction());
```

When this is enabled, Laravel will throw an exception whenever lazy loading occurs. This makes unintended relationship access obvious during development and helps ensure your queries stay efficient before reaching production.