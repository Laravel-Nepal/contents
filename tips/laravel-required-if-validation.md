---
title: Use required_if for Conditional Validation in Laravel
slug: laravel-required-if-validation
author_username: dipesh79
date: 2026-04-13
tags: ["laravel", "validation", "requests"]
---

Laravel’s `required_if` validation rule lets you conditionally require a field based on the value of another field. This is useful when certain inputs are only needed in specific scenarios.

### Example

```php
use App\Enums\PaymentMethod;

'paymentMethod' => ['nullable', 'string', Rule::in(PaymentMethod::class)],
'paymentReceipt' => ['required_if:paymentMethod,' . PaymentMethod::COD->value,'file']
```

In this case, `paymentReceipt` is only required when the `paymentMethod` is `cod`.

### Why use it

- Keeps validation logic clean and declarative
- Avoids manual condition checks in controllers
- Makes request rules easier to understand and maintain

### When to use

Use `required_if` when a field should only be required based on another field’s value, such as payment methods, feature flags, or optional workflows.

It helps keep your validation logic expressive while reducing unnecessary complexity.