---
title: Check if Filament is Serving the Current Request
slug: filament-is-serving
author_username: achyut
date: 2025-12-26
tags: ["filament", "laravel", "php"]
---

If you want to write a logic that should only run within or outside Filament, you can use either of the two methods below:

```php
// Method 1: Using the Filament Facade
use Filament\Facades\Filament;

Filament::isServing();

// Method 2: Using the Filament Helper Function
filament()->isServing();
```

Both methods return a boolean indicating whether Filament is currently serving a request. This helps you separate logic that should only execute within the Filament admin panel from the rest of your application.