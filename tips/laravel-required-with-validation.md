---
title: Use required_with for Dependent Fields in Laravel Validation
slug: laravel-required-with-validation
author_username: dipesh79
date: 2026-04-16
tags: ["laravel", "validation", "requests"]
---

Laravel’s `required_with` validation rule makes a field required only when one or more related fields are present. This is useful when inputs depend on each other.

### Example

```php
'coupon_code' => ['nullable', 'string'],
'coupon_discount' => ['required_with:coupon_code', 'numeric'],
```

In this case, `coupon_discount` is required only if `coupon_code` is provided.

### Why use it

- Keeps related fields in sync
- Avoids partial or inconsistent data
- Removes the need for manual conditional checks

### When to use

Use `required_with` when a field should only be required if another field exists, such as paired inputs, optional groups, or dependent form fields.

It helps maintain clean and predictable validation rules without extra logic.