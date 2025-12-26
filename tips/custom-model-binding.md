---
title: Use Custom Columns for Model Binding in Laravel Routes
slug: custom-model-binding
author_username: achyut
date: 2025-12-26
tags: ["laravel", "routes", "model binding"]
canonical_url: https://example.com/your-post-url
---

Sometimes, you may want to use columns other than `id` for model binding in your Laravel routes. There are two ways to handle this:

1. **Specify the column while binding in the routes file.** This sets the binding key within the scope of this specific route.
2. **Add the route key to the Eloquent model** using the `getRouteKeyName()` method. This will always search for a model using the specified column (unless method 1 is used).

Which method do you prefer if you already know both?

```php
// Method 1

// web.php
Route::get('/{order:order_number}', function (Order $order) {
    return $order;
});

// Method 2

// Order.php
public function getRouteKeyName(): string
{
    return 'order_number';
}

// web.php
Route::get('/{order}', function (Order $order) {
    return $order;
});
```