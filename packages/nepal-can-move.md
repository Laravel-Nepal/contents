---
name: Nepal Can Move
author_username: achyut
github: Laravel-Nepal/nepal-can-move-php
packagist: laravel-nepal/nepal-can-move
tags: ["nepal", "delivery", "sdk", "php"]
---

This package provides a fluent wrapper around the [Nepal Can Move](https://nepalcanmove.com) API, normalizing API responses into clean **Data Transfer Objects (DTOs)**, leveraging PHP Enums for type safety, and handling all authentication and error states automatically.

## Install

```bash
composer require laravel-nepal/nepal-can-move
```

## Usage

```php
use LaravelNepal\NCM\Data\CreateOrderRequest;
use LaravelNepal\NCM\Enums\DeliveryType;

$request = new CreateOrderRequest(
    name: 'Achyut Neupane',
    phone: '9800000000',
    codCharge: '1500',
    address: 'Lakeside, Pokhara',
    sourceBranch: 'KATHMANDU',
    destinationBranch: 'POKHARA',
    package: 'Books',
    deliveryType: DeliveryType::DoorToDoor
);

$order = NCM::createOrder($request);

echo $order->id;
```

For more details, check the [GitHub repository](https://github.com/Laravel-Nepal/nepal-can-move-php).