---
title: Prevent Negative Values When Updating Counters in Laravel
author_username: achyut
date: 2025-01-03
tags: ["laravel", "eloquent", "tips"]
---

When building real-world systems, you often deal with counters. Things like remaining API credits, product quantity, available seats, or usage limits. At first glance, reducing a number feels trivial.

Problems appear when edge cases hit. Concurrent requests, retries, or unexpected input can push values below zero. Once that happens, your data no longer represents reality.

A simple rule keeps your data sane: **a counter should never go below zero**.

## Safe counter update with Eloquent

Below is an example using remaining API credits instead of inventory.

```php
use App\Models\ApiClient;

$client = ApiClient::query()->lockForUpdate()->findOrFail($clientId);
$usedCredits = 15;

// Always validate before updating
if ($usedCredits <= 0) {
    return;
}

$currentCredits = $client->remaining_credits;

$client->remaining_credits = max(0, $currentCredits - $usedCredits);
$client->save();
```

### Why this approach works

- `lockForUpdate()` for concurrency safety
- `max(0, ...)` ensures the value never drops below zero
- Early return for invalid input

This pattern is small, readable, and reliable. Use it anywhere a value must never fall below zero.

