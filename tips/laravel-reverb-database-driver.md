---
title: Use Database Driver for Broadcasting in Laravel Reverb
slug: laravel-reverb-database-driver
author_username: dipesh79
date: 2026-04-01
tags: ["laravel", "reverb", "broadcasting"]
---

Laravel Reverb allows you to use the database as a broadcast driver, removing the need for Redis in simpler setups.

### Configuration

Previously, broadcasting typically required Redis:

```env
BROADCAST_DRIVER=redis
```

Now you can use the database driver:

```env
BROADCAST_DRIVER=database
```

### Example

```php
use Illuminate\\Mail\\Events\\MessageSent;

broadcast(new MessageSent($message))->toOthers();
```

### Why use it

- No need to set up Redis or additional infrastructure
- Lower cost for small projects and startups
- Simpler configuration and maintenance

This is a practical option when you need real-time features without adding extra services to your stack.