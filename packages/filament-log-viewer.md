---
name: Filament Log Viewer
author_username: achyut
github: achyutkneupane/filament-log-viewer
packagist: achyutn/filament-log-viewer
tags: ["laravel", "filament", "log viewer"]
---

A [Filament](https://filamentphp.com/) plugin to read and display Laravel log files in a clean, searchable table with stack traces and filtering.

## Installation

```bash
composer require achyutn/filament-log-viewer
```

Register the plugin inside your Filament panel:

```php
use AchyutN\FilamentLogViewer\FilamentLogViewer;

return $panel
    ->plugins([
        FilamentLogViewer::make(),
    ]);
```

View more on Filament's [plugin page](https://filamentphp.com/plugins/achyutn-log-viewer).