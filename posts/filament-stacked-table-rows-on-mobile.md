---
title: Stacked Table Rows on Mobile in Filament
description: Filament now supports stacking table rows on mobile, making tables more readable on small screens without losing desktop usability.
author_username: achyut
date: 2026-02-06
tags: ["filament", "laravel", "responsive design"]
---

Filament [v5.2.0](https://github.com/filamentphp/filament/releases/tag/v5.2.0) (along with [v4.7.0](https://github.com/filamentphp/filament/releases/tag/v4.7.0)) introduces a practical improvement for table responsiveness: **stacked table rows on mobile**.

The new `stackedOnMobile()` method allows developers to easily enable this behavior for any Filament table. This change improves readability on small screens while preserving the traditional table layout on larger displays.

This feature was introduced in [Pull Request #19113](https://github.com/filamentphp/filament/pull/19113) by [Martijn Wagena ](https://github.com/mwagena), and it marks their first contribution to Filament.

## Why This Matters

Tables can be hard to read on mobile devices because horizontal layouts often require side-scrolling. With stacked rows:

- Column labels appear above their values in a card-style format.
- Sorting and bulk actions remain functional and accessible.
- Users get a clean, readable table without extra CSS or HTML adjustments.

## The problem with tables on mobile

Tables are inherently horizontal. On mobile screens, this often results in cramped columns or forced horizontal scrolling. While scrolling can work, it quickly becomes frustrating for data-heavy admin interfaces.

The existing `Split` and `Stack` components in Filament allow for custom responsive layouts, but they require manual configuration and may not be ideal for simple tables.

Filament now offers a built-in way to adapt tables for smaller screens without custom CSS or layout workarounds.

## Simple mobile stacking with `stackedOnMobile()`

The fastest way to make a table responsive is by enabling stacking directly on the table:

```php
use Filament\Tables\Columns\TextColumn;
use Filament\Tables\Table;

public function table(Table $table): Table
{
    return $table
        ->columns([
            TextColumn::make('title')
                ->searchable(),
            TextColumn::make('type')
                ->badge()
                ->searchable(),
            TextColumn::make('views_count')
                ->label('Views')
                ->counts('views')
                ->badge()
        ])
        ->stackedOnMobile();
}
```

## How it looks

- **Desktop**: Standard table layout  
![Desktop Preview of the stacked table](https://hamrocdn.com/gJV4A2wZVtL0)

- **Mobile**: Each row displays as a card with the column label above the value
![Mobile Preview of the stacked table](https://hamrocdn.com/5BWwn3fgLZD3)

- **Sortable columns**: A sort dropdown appears at the top of the table
![Sortable columns on mobile](https://hamrocdn.com/u3sp6bzMmkoC)

- **Bulk selection**: Checkbox appears at the top for mobile rows
![Bulk selection on mobile](https://hamrocdn.com/fGmrzESmhC0x)

This method is ideal for simple responsive tables without restructuring your column layout.

## Advanced Layouts

For more control over table layouts on mobile and desktop, Filament provides `Split` and `Stack` components since v2.

- **Split**: Wraps columns to display side-by-side on larger screens and stack them below a responsive breakpoint.
- **Stack**: Groups multiple columns vertically inside a split or standalone layout.

```php
use Filament\Tables\Columns\Layout\Split;
use Filament\Tables\Columns\Layout\Stack;
use Filament\Tables\Columns\TextColumn;

Split::make([
    TextColumn::make('title')
        ->sortable()
        ->searchable(),
    Stack::make([
        TextColumn::make('type')
            ->badge()
            ->searchable(),
        TextColumn::make('views_count')
            ->label('Views')
            ->counts('views')
            ->badge(),
    ]),
]);
```

Output:

![Split and Stack layout on desktop](https://hamrocdn.com/oHSLq3etuQaN)

![Split and Stack layout on mobile](https://hamrocdn.com/yUFw2R9EwKAO)

This gives developers fine-grained control over table layouts, while `stackedOnMobile()` provides a simple, automatic mobile-friendly solution.

## Summary
- New: `stackedOnMobile()` makes tables readable on mobile without breaking desktop layout.
- Existing: `Split` and `Stack` remains for custom responsive layouts.

This change enhances Filament's responsiveness and usability, making it easier to build admin interfaces that work well on all devices with minimal effort.

---

**Pull Request:** [#19113](https://github.com/filamentphp/filament/pull/19113)  
**Author:** [Martijn Wagena](https://github.com/mwagena)
