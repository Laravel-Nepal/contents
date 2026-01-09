---
name: Laravel News SDK
author_username: achyut
github: achyutkneupane/laravel-news-sdk
packagist: achyutn/laravel-news-sdk
tags: ["laravel", "sdk", "news", "api", "php"]
---

PHP SDK for publishing links to **laravel-news.com** through its official API.

## Install

```bash
composer require achyutn/laravel-news-sdk
```

## Usage

```php
use AchyutN\LaravelNews\Facades\LaravelNews;
use AchyutN\LaravelNews\Data\Link;
use AchyutN\LaravelNews\Enums\LinkCategory;

$link = new Link(
    title: 'Laravel News SDK',
    url: 'https://laravelnepal.com/package/laravel-news-sdk',
    category: LinkCategory::Package
);

$item = LaravelNews::post($link);

echo $item->id;
echo $item->title;
```

For more details, check the [GitHub repository](https://github.com/achyutkneupane/laravel-news-sdk).