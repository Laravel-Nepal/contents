---
title: Unscope a Filament Resource in a Multi-Tenancy Panel
slug: filament-tenancy-unscoped-resource
author_username: achyut
date: 2025-09-02
tags: ["filament", "multi-tenancy", "laravel", "php"]
---

When working with multi-tenancy in Filament, you might encounter scenarios where you need to exclude a resource from the current tenant context. This is particularly useful for resources that should be accessible across all tenants, such as global settings or shared data.

Set `$isScopedToTenant` to `false` to in the resource class to un-scope it from the tenant context.

```php
final class UserResource extends Resource
{
    protected static bool $isScopedToTenant = false;
}
```
