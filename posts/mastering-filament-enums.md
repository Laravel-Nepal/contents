---
title: Mastering Filament Enums - Labels, Colors, Icons, and Descriptions
slug: mastering-filament-enums
author_username: achyut
date: 2025-12-26
tags: ["laravel", "filament", "enums", "php"]
---

Being part of the [Laravel](https://laravel.com) ecosystem encourages clean, fast development. [Filament](https://filamentphp.com) is a go-to package for building modern admin panels in Laravel. One of the key patterns in Filament is leveraging **PHP enums** with specialized interfaces to enhance your admin panels.

Enums are special PHP classes representing a fixed set of constants, like statuses, roles, or categories. Filament provides interfaces like `HasLabel`, `HasColor`, `HasIcon`, and `HasDescription` to make enums interactive in your UI.

## Enum Labels

The `HasLabel` interface converts an enum instance into a human-readable label. It’s used in forms, tables, and infolists.

```php
use Filament\Support\Contracts\HasLabel;

enum UserRole: string implements HasLabel
{
    case Admin = 'admin';
    case Moderator = 'moderator';
    case User = 'user';

    public function getLabel(): ?string
    {
        return match ($this) {
            self::Admin => 'Administrator',
            self::Moderator => 'Moderator',
            self::User => 'User',
        };
    }
}
```

## Enum Colors

The `HasColor` interface allows enums to display colored values in the UI.

```php
use Filament\Support\Contracts\HasColor;

enum UserRole: string implements HasColor
{
    case Admin = 'admin';
    case Moderator = 'moderator';
    case User = 'user';

    public function getColor(): string | array | null
    {
        return match ($this) {
            self::Admin => 'red',
            self::Moderator => 'yellow',
            self::User => 'blue',
        };
    }
}
```

Or use the `Color` enum:

```php
use Filament\Support\Colors\Color;

public function getColor(): string | array | null
{
    return match ($this) {
        self::Admin => Color::Red,
        self::Moderator => Color::Yellow,
        self::User => Color::Blue,
    };
}
```

## Enum Icons

The `HasIcon` interface lets enums display icons next to their labels.

```php
use Filament\Support\Contracts\HasIcon;

enum UserRole: string implements HasIcon
{
    case Admin = 'admin';
    case Moderator = 'moderator';
    case User = 'user';

    public function getIcon(): ?string
    {
        return match ($this) {
            self::Admin => 'heroicon-m-shield-check',
            self::Moderator => 'heroicon-m-flag',
            self::User => 'heroicon-m-user',
        };
    }
}
```
Or use the `Heroicon` enum:

```php
use Filament\Support\Icons\Heroicon;

public function getIcon(): ?string
{
    return match ($this) {
        self::Admin => Heroicon::ShieldCheck,
        self::Moderator => Heroicon::Flag,
        self::User => Heroicon::User,
    };
}
```

Icons appear automatically in tables, infolists, and toggle buttons when cast to an enum.

## Enum Descriptions

The `HasDescription` interface adds textual descriptions to enum values.

```php
use Filament\Support\Contracts\HasLabel;
use Filament\Support\Contracts\HasDescription;

enum UserRole: string implements HasLabel, HasDescription
{
    case Admin = 'admin';
    case Moderator = 'moderator';
    case User = 'user';

    public function getLabel(): ?string
    {
        return $this->name;
    }

    public function getDescription(): ?string
    {
        return match ($this) {
            self::Admin => 'Full access to all system features.',
            self::Moderator => 'Can manage content and moderate users.',
            self::User => 'Regular user with limited access.',
        };
    }
}
```

Using Laravel enums with these interfaces enhances your Filament panels with **human-readable labels, colors, icons, and descriptions**, keeping your code clean and maintainable.

