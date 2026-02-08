---
title: Generate Pixel-Perfect Dynamic Thumbnails in Laravel
description: AI is great, but for data accuracy, you need code. Learn how to convert HTML + CSS into dynamic, high-quality images using Laravel and Browsershot.
author_username: achyut
date: 2026-02-09
tags: ["laravel", "browsershot", "automation", "thumbnail"]
---

In the age of AI, it's easy to get caught up in the hype and forget that sometimes, the best solution is a good old-fashioned code. Tools like DALL·E and Midjourney are fantastic for generating creative images, but when it comes to data accuracy and consistency, they can fall short. If you need to create dynamic thumbnails that reflect specific data or content, relying on AI-generated images might not be the best approach.

In this post, we’re going to build a **HTML-to-Image** service using **Laravel** and **Spatie's [Browsershot](https://github.com/spatie/browsershot)**. This service will allow us to generate images from HTML and CSS, perfect for creating dynamic thumbnails for social media, blog posts, or any other content.

The [thumbnails](https://laravelnepal.com/open-graph/post/dynamic-thumbnails-in-laravel) we use in [Laravel Nepal](https://laravelnepal.com) are generated using this technique. The code we use is open source and are available [here](https://github.com/Laravel-Nepal/laravelnepal.com/blob/master/app/Http/Controllers/RenderOpenGraphImage.php).

## The Concept

The idea is simple: we will create an HTML template that represents our thumbnail design. This template can include dynamic data, such as titles, descriptions, and images. Then, we will use Browsershot to render this HTML into a high-quality image.

The workflow:

1. **Design** a standard HTML+CSS page using Blade.
2. **Inject** your data into that view.
3. **Snapshot** the rendered HTML using a headless Chrome browser via a Service class.
4. **Save** the image to storage and return the path.

## Setting Up

First, make sure you have Laravel installed and set up. Then, install the Browsershot package:

```bash
composer require spatie/browsershot
```

Browsershot relies on [Puppeteer](https://pptr.dev) (a Node library), so your server needs Node.js and NPM installed.

```bash
npm install puppeteer
```

Or, you can install it globally:

```bash
npm install -g puppeteer
```

## The "Weekly Stats" Example

Instead of a standard blog post header, let's build something dynamic: a **Weekly Analytics Share Card** for a SaaS application. This is the kind of image your users would love to share on Twitter/X to show off their progress.

![Example of a Weekly Analytics Share Card](https://hamrocdn.com/cA8zj7ppAG2-)

Above is the example of a share card that displays a user's weekly stats. The design is clean and visually appealing, making it perfect for sharing on social media.

### Blade View

First, we start with a simple blade file with texts. Initially, our goal is to generate dynamic image without any styles. For that, let's create a Blade view for our image. Create a new file at `resources/views/analytics-card.blade.php`:

```blade
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Analytics Card</title>
</head>
<body>
<div>
    <div>
        <img src="{{ $stats['avatar'] }}" alt="User Avatar" width="100" height="100">
        <h1>{{ $stats['name'] }}</h1>
        <p>Weekly Performance Report</p>
    </div>

    <ul>
        <li>
            <strong>Total Views:</strong> {{ $stats['views'] }}
        </li>
        <li>
            <strong>Conversion:</strong> {{ $stats['conversion_rate'] }}
        </li>
        <li>
            <strong>Revenue:</strong> {{ $stats['revenue'] }}
        </li>
    </ul>

    <div>
        <p>Period: {{ $stats['week'] }}</p>
        <p>laravelnepal.com</p>
    </div>
</div>
</body>
</html>
```

This will look like this:

![Basic HTML Thumbnail Template](https://hamrocdn.com/jpl2cjGWFCm1)

The blade looks basic and unstyled, but it serves our purpose of generating a dynamic image. We will add styles later to make it look more appealing.

### The Image Generation Service

Instead of cluttering our controllers, we will create a dedicated service class to handle the image generation. Create a new file at `app/Services/ThumbnailService.php`.

This service handles the directory creation, checks if the image already exists, and performs the HTML-to-Image conversion.

```php
<?php

namespace App\Services;

use App\Models\User;
use Illuminate\Support\Facades\Storage;
use Spatie\Browsershot\Browsershot;

class ThumbnailService
{
    public function generate(User $user, bool $force = false): string
    {
        $weekHash = now()->startOfWeek()->format('Ymd');
        $fileName = "analytics/user-{$user->id}-{$weekHash}.png";

        if (!$force && Storage::disk('public')->exists($fileName)) {
            return Storage::path($fileName);
        }

        $stats = [
            'views' => number_format($user->views_count),
            'conversion_rate' => $user->conversion_rate,
            'revenue' => '$' . number_format($user->weekly_revenue, 2),
            'avatar' => $user->avatar_url,
            'name' => $user->name,
            'week' => now()->startOfWeek()->format('M d') . ' - ' . now()->endOfWeek()->format('M d'),
        ];

        $html = view('analytics-card', compact('stats'))->render();

        $fullPath = Storage::disk('public')->path($fileName);
        $directory = dirname($fullPath);
        if (!is_dir($directory)) {
            mkdir($directory, 0755, true);
        }

        Browsershot::html($html)
            ->windowSize(1200, 630)
            ->deviceScaleFactor(2)
            ->waitUntilNetworkIdle()
            ->setNodeBinary(config('services.node.node_path', '/usr/bin/node'))
            ->setNpmBinary(config('services.node.npm_path', '/usr/bin/npm'))
            ->noSandbox()
            ->save($fullPath);

        return Storage::path($fileName);
    }
}
```

#### Breaking Down Browsershot Options

- `windowSize(1200, 630)`: ets the viewport size. This is the standard Open Graph size for Twitter and Facebook. If your content flows outside this box, it won't be captured.
- `deviceScaleFactor(2)`: Simulates a Retina display. This captures twice as many pixels, making text and vectors crisp. Without this, text can look fuzzy or pixelated. Available options are: `1`, `2`, and `3`.
- `waitUntilNetworkIdle()`: Tells the browser to wait until all network requests (images, fonts, CSS) have finished loading before taking the screenshot. Crucial if you load external fonts or avatars.
- `setNodeBinary()` and `setNpmBinary()`: Explicitly tells PHP where Node is installed. On local machines, PHP often finds it automatically. On servers (Ubuntu/Nginx), the `www-data` user often doesn't have Node in its `$PATH`, so you must set this manually.
- `noSandbox()`: This is often required in server environments where the sandbox may not work properly. However, use this with caution as it can have security implications.
- `save($fullPath)`: Instructs Browsershot to write the file to the disk rather than just returning the binary data.

> If you want to return the image directly as a response instead of saving it, you can use `->screenshot()` instead of `->save()`, and return the binary data with the appropriate headers.

### Using the Service in a Controller

Now, let's use our `ThumbnailService` in a controller. Create a new controller or use an existing one. Here’s an example of how to use the service:

```php
class ShowAnalyticsCardController extends Controller
{
    public function __invoke(User $user, ThumbnailService $generator)
    {
        $url = $generator->generate($user);

        return response()->file($url, [
            'Content-Disposition' => 'inline; filename="'.$user->id.'.png"',
            'Cache-Control' => 'public, max-age=604800, immutable',
        ]);
    }
}
```

### Results

Now, when you hit the route that points to `ShowAnalyticsCardController`, it will generate a dynamic thumbnail based on the user's data and return it as an image response.

You can use the service whenever you need to generate a thumbnail, whether for social sharing, email previews, or any other purpose.

### Styling the Thumbnail

To make our thumbnail visually appealing, we can add CSS styles to our Blade view. Here’s an updated version of `analytics-card.blade.php` with the style matching the first image:

```blade
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <style>
        @import url('[https://fonts.googleapis.com/css2?family=Outfit:wght@300;600;800&display=swap](https://fonts.googleapis.com/css2?family=Outfit:wght@300;600;800&display=swap)');

        * { margin: 0; padding: 0; box-sizing: border-box; }

        body {
            width: 1200px;
            height: 630px;
            font-family: 'Outfit', sans-serif;
            background-color: #0f172a; /* Slate 900 */
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
            position: relative;
        }

        /* Abstract Background Shapes */
        .blob {
            position: absolute;
            border-radius: 50%;
            filter: blur(80px);
            opacity: 0.6;
            z-index: 0;
        }
        .blob-1 { width: 500px; height: 500px; background: #6366f1; top: -100px; left: -100px; } /* Indigo */
        .blob-2 { width: 400px; height: 400px; background: #ec4899; bottom: -50px; right: -50px; } /* Pink */
        .blob-3 { width: 300px; height: 300px; background: #06b6d4; bottom: 50px; left: 30%; } /* Cyan */

        .card {
            width: 1050px;
            height: 480px;
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 40px;
            z-index: 10;
            padding: 60px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
        }

        /* Header */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
        }

        .user-profile {
            display: flex;
            align-items: center;
            gap: 25px;
        }

        .avatar {
            width: 100px;
            height: 100px;
            border-radius: 25px;
            border: 4px solid rgba(255,255,255,0.2);
            object-fit: cover;
        }

        .user-info h1 { font-size: 36px; font-weight: 800; line-height: 1.1; }
        .user-info p { font-size: 20px; color: #94a3b8; margin-top: 5px; font-weight: 300; }

        .logo-badge {
            background: #ffffff;
            color: #0f172a;
            padding: 12px 24px;
            border-radius: 12px;
            font-weight: 800;
            font-size: 20px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 40px;
            margin-top: 20px;
        }

        .stat-item {
            background: rgba(0, 0, 0, 0.2);
            border-radius: 24px;
            padding: 30px;
            border-left: 4px solid #6366f1;
        }

        .stat-item:nth-child(2) { border-color: #ec4899; }
        .stat-item:nth-child(3) { border-color: #06b6d4; }

        .stat-label {
            color: #94a3b8;
            font-size: 18px;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 10px;
            font-weight: 600;
        }

        .stat-value {
            font-size: 52px;
            font-weight: 800;
            color: #ffffff;
        }

        .footer {
            margin-top: auto;
            border-top: 1px solid rgba(255,255,255,0.1);
            padding-top: 25px;
            display: flex;
            justify-content: space-between;
            color: #cbd5e1;
            font-size: 18px;
        }
    </style>
</head>
<body>
<div class="blob blob-1"></div>
<div class="blob blob-2"></div>
<div class="blob blob-3"></div>

<div class="card">
    <div class="header">
        <div class="user-profile">
            <img src="{{ $stats['avatar'] }}" class="avatar" alt="User">
            <div class="user-info">
                <h1>{{ $stats['name'] }}</h1>
                <p>Weekly Performance Report</p>
            </div>
        </div>
        <div class="logo-badge">
            laravelnepal.com
        </div>
    </div>

    <div class="stats-grid">
        <div class="stat-item">
            <div class="stat-label">Total Views</div>
            <div class="stat-value">{{ $stats['views'] }}</div>
        </div>
        <div class="stat-item">
            <div class="stat-label">Conversion</div>
            <div class="stat-value">{{ $stats['conversion_rate'] }}</div>
        </div>
        <div class="stat-item">
            <div class="stat-label">Revenue</div>
            <div class="stat-value">{{ $stats['revenue'] }}</div>
        </div>
    </div>

    <div class="footer">
        <span>Period: {{ $stats['week'] }}</span>
    </div>
</div>
</body>
</html>
```
This will give you a beautifully styled thumbnail that looks great when shared on social media.

## Common Errors & Troubleshooting

Generating images involves a full browser stack, so things can go wrong. Here are the most common issues:

1. "The command 'node' failed"  
**Cause**: The user running PHP (e.g., `www-data`) cannot find the Node executable.  
**Fix**: Verify your node path by running `which node` in your terminal, and hardcode that path in `setNodeBinary()` or set it in your config. Same applies to NPM.

2. Images or Fonts are missing  
**Cause**: The browser took the screenshot before all assets loaded.  
**Fix**: Add `->setDelay(1000)` (wait 1 second) or ensure you are using `waitUntilNetworkIdle()`. Also, ensure your image paths are absolute URLs (e.g., http://yoursite.com/storage/logo.png), not relative paths (`/storage/logo.png`).

3. Blurry Text  
**Cause**: The default device scale factor is 1, which can make text look pixelated.  
**Fix**: Set `->deviceScaleFactor()` to `2` or even `3` to simulate a Retina display, which captures more pixels and makes text crisp.

4. Timeout Error  
**Cause**: The browser is taking too long to load the page or render the screenshot.  
**Fix**: Increase the timeout limit: `->timeout(120)`. If you are on a weak server, Puppeteer might just be too heavy; consider upgrading RAM.

## Conclusion

By treating your images as code, you gain version control, consistency, and the ability to update your entire brand's visual style by changing a single Blade file.

This approach is perfect for generating dynamic thumbnails that need to reflect specific data or content. Whether you're creating shareable social media cards, email previews, or any other type of dynamic image, using Laravel and Browsershot gives you the power and flexibility to create pixel-perfect results every time.
