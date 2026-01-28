---
title: Collections gets hasSole() method in Laravel 12.49
description: Laravel 12.49 introduces the hasSole() collection method, a clearer and more expressive replacement for containsOneItem() with full filter support.
author_username: achyut
date: 2026-01-28
tags: ["laravel", "php", "collection"]
---

Laravel 12.49 introduces a new `hasSole()` method to the Collection and LazyCollection classes, providing a concise and expressive way to check if a collection contains **exactly one item**, optionally filtered by a condition. This improves readability and replaces the older, now-deprecated `containsOneItem()` method.

## What `hasSole()` Does

The `hasSole()` method evaluates whether a collection contains a single item. You can optionally provide a filter to match specific criteria. Unlike `sole()`, which throws an exception if the condition isn’t met, `hasSole()` simply returns a boolean.

This makes it ideal for conditional checks without needing try/catch blocks or additional counting logic.

## Filter Support

`hasSole()` supports the same filtering capabilities as the existing `sole()` method:

- **No filter:** Checks if the collection has exactly one item.
- **Callback filter:** Pass a closure to determine if the item should be counted.
- **Key-value filter:** Match items based on a given key and value.
- **Full operator syntax:** Use comparison operators like `>=`, `<`, etc.

## Examples

### Basic usage

Check if a collection has exactly one item:

```php
$users = collect(['Alice']);

if ($users->hasSole()) {
    echo "Exactly one user exists.";
}
```

### Using a callback

Check for a single active user:

```php
$users = collect([
    ['name' => 'Alice', 'active' => true],
    ['name' => 'Bob', 'active' => false],
]);

if ($users->hasSole(fn ($user) => $user['active'])) {
    echo "Exactly one active user found.";
}
```

### Using key-value filtering

Check for a single user with a specific status:

```php
$orders = collect([
    ['id' => 1, 'status' => 'pending'],
    ['id' => 2, 'status' => 'completed'],
]);

if ($orders->hasSole('status', 'pending')) {
    echo "Exactly one pending order found.";
}
```

### Using an operator

Check for a single user over a certain age:

```php
$users = collect([
    ['name' => 'Alice', 'age' => 22],
    ['name' => 'Bob', 'age' => 25],
]);

if ($users->hasSole('age', '>=', 25)) {
    echo "Exactly one user aged 25 or older.";
}
```

## LazyCollection Support

For `LazyCollection`, `hasSole()` preserves lazy evaluation. It stops processing once it determines that the collection either contains more than one matching item or none, keeping performance efficient even for large datasets.

```php
LazyCollection::times(1000)
    ->hasSole(fn ($item) => $item <= 1);
```

## Deprecation of `containsOneItem()`

The older `containsOneItem()` method now delegates to `hasSole()` and is **deprecated**. This ensures backwards compatibility while encouraging the new, clearer API.

```php
$users->containsOneItem(); // deprecated
$users->hasSole();         // preferred
```

In Laravel 13, `containsOneItem()` will be removed entirely, and `hasSole()` will be added to the `Enumerable` interface.

## Summary

The `hasSole()` method simplifies checking for single items in a collection:

- Provides a short and expressive way to check for exactly one item.
- Supports callbacks, key/value filters, and operators.
- Works with both `Collection` and `LazyCollection`.
- Replaces the deprecated `containsOneItem()` method.
- Returns a boolean instead of throwing exceptions, unlike `sole()`.

This change makes collection handling more readable and aligns method naming with existing `sole()` conventions.

---

**Pull Request:** [#58463](https://github.com/laravel/framework/pull/58463)  
**Author:** [Joseph Silber](https://github.com/JosephSilber)
