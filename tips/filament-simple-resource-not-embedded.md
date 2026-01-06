---
title: Generate Form, Infolist, and Table file for simple resource in Filament
description: A method for creating a simple resource in Filament with separate files for form, infolist, and table components instead of embedding them within the resource file.
slug: filament-simple-resource-not-embedded
author_username: achyut
date: 2026-01-06
tags: ["filament", "laravel", "php", "resource", "simple"]
---

When creating a simple resource in Filament, you get a single resource file that contains all the necessary configurations for forms, tables, and infolists. However, if you prefer to have separate files for each component (form, table, infolist) instead of having them embedded within the resource file, you can achieve this by manually creating the files and updating the resource accordingly.

Add `--not-embedded` flag along with `--simple` flag while creating a resource using the Filament Artisan command:

```bash
php artisan make:filament-resource Blog --simple --not-embedded
```

This command will generate a resource named `Blog` with separate files for the form, table, and infolist components. You can then customize each of these files independently.