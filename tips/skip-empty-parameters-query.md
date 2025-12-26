---
title: Skip Empty Parameters in Laravel Query Builder
slug: skip-empty-parameters-query
author_username: achyut
date: 2025-12-26
tags: ["laravel", "query builder"]
---

When building search or filter queries in Laravel, you can skip empty parameters using the `when()` method.

This prevents unnecessary `where` clauses when a variable is empty.

```php
$products = Product::query()
    ->when($filterValues, function ($query, $filterValues) {
        return $query->whereIn('column_name', $filterValues);
    })
    ->get();
```