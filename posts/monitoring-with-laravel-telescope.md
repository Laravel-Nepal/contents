---
title: Stop Guessing, Start Observing - Laravel Telescope
description: Laravel Telescope is a real-time debug dashboard that captures requests, queries, jobs, and exceptions. Learn how to install it, configure it safely for production, and use it to spot redundant queries and debug issues instantly.
author_username: samaya
date: 2026-02-18
tags: ["laravel", "telescope", "monitoring", "debugging"]
---

> *Deploying Laravel apps, but blind to what is happening? When production breaks or requests crawl, you are still hunting through logs and sprinkling `dd()` everywhere. There is a better way.*

---

## The Problem

Every Laravel dev has been there: a request takes 3 seconds too long, a job silently fails, a query runs 47 times instead of once. You suspect N+1 but have no proof.

The typical workflow? `Log::info()` scattered everywhere, tailing files, `dd()` destroying the request, raw stack traces with zero context. It's tedious, slow, and doesn't scale.

**What you need is observability**: Seeing *everything* your app does: requests, queries, jobs, exceptions, mail. Real-time. That is **Laravel Telescope**.

---

## What Is Laravel Telescope?

**Laravel Telescope** is a real-time debug dashboard for Laravel. Think of it as **DevTools for your backend**. Instead of raw logs, you get a clean, searchable UI showing every request, query, exception, and job with full context.

It runs on your server, stores data in your database, no third-party services. Just install it and it silently records everything.

---

## What It Captures (The Good Stuff)

- **Requests** — URL, method, headers, response status, timing
- **Queries** — SQL, bindings, execution time (N+1 problems are obvious instantly)
- **Exceptions** — Full stack trace + request context
- **Jobs** — Dispatch time, queue, success/failure, attempts
- **Mail & Notifications** — Full rendered preview
- **Cache, Redis, Models, Commands** — Complete visibility
- **Slow queries** — Automatically highlighted

---

## Real Impact

- Spot N+1 queries in seconds
- Debug failed jobs without log archaeology  
- See exactly what an API endpoint does (all queries, cache hits, models loaded)
- Catch slow queries before production
- Preview emails right in the dashboard

---

## Installation (5 Minutes)

```bash
composer require laravel/telescope
php artisan telescope:install
php artisan migrate
# Visit http://your-app.test/telescope
```

Done. Start making requests and watch Telescope capture everything.

---

## Important Things to Know: Development vs. Production

This is the section that most tutorials skip, and it is critically important. **Telescope is a double-edged sword if you are not careful about how it is configured.**

### In Development: Use It Freely
In your local environment, Telescope is your best friend. Enable all watchers, keep the dashboard open in a browser tab alongside your app, and let it capture everything. There is no performance concern, no data sensitivity issue, and no reason to hold back.

### In Production: Proceed With Caution

Here's where things get nuanced.

**Performance overhead is real.** Every request, query, and job that Telescope watches involves writing records to your database. Under heavy traffic, this can add measurable overhead. Telescope stores its data in `telescope_entries` and related tables, which can grow very large very quickly on a busy application.

**The database will fill up.** Telescope keeps its own pruning schedule, but if you forget to run `telescope:prune` regularly (ideally via your scheduler), you will end up with millions of rows slowing down your database. Always configure pruning:

```php
// In app/Console/Kernel.php (Laravel 10 and below)
$schedule->command('telescope:prune')->daily();

// Laravel 11+ (in routes/console.php)
Schedule::command('telescope:prune')->daily();
```

**Sensitive data is captured.** Telescope records request bodies, session data, and headers. If your users submit passwords or sensitive information, that data may end up in the Telescope database. You should configure Telescope to hide sensitive keys:

```php
// In TelescopeServiceProvider::boot()
Telescope::hideRequestParameters(['password', 'password_confirmation', 'credit_card']);
Telescope::hideSensitiveRequestDetails(); // Hides body/headers in non-local environments
```

**Lock it down.** The Telescope dashboard at `/telescope` should never be publicly accessible in production. By default, Telescope ships with a gate that restricts access to specific email addresses. Customize this in `TelescopeServiceProvider`:

```php
protected function gate(): void
{
    Gate::define('viewTelescope', function ($user) {
        return in_array($user->email, [
            'yourname@yourapp.com',
        ]);
    });
}
```

You can also use the `TELESCOPE_ENABLED` environment variable to disable Telescope entirely in production and only enable it when actively debugging:

```env
TELESCOPE_ENABLED=false
```

**Consider environment-specific installation.** The Telescope documentation recommends installing it with the `--dev` flag so it's only included in development dependencies:

```bash
composer require laravel/telescope --dev
```

Then in `AppServiceProvider`, only register Telescope in non-production environments:

```php
public function register(): void
{
    if ($this->app->isLocal()) {
        $this->app->register(\Laravel\Telescope\TelescopeServiceProvider::class);
        $this->app->register(TelescopeServiceProvider::class);
    }
}
```


---

## Laravel Versions Supported by Telescope

Laravel Telescope has kept pace with the framework itself:

- **Telescope 5.x** — Laravel 12.x — PHP 8.3+
- **Telescope 5.x** — Laravel 11.x — PHP 8.2+
- **Telescope 4.x** — Laravel 9.x – 10.x — PHP 8.0+
- **Telescope 3.x** — Laravel 8.x — PHP 7.3+
- **Telescope 2.x** — Laravel 7.x — PHP 7.2.5+
- **Telescope 1.x** — Laravel 5.7 – 6.x — PHP 7.1+

Always check the [official Telescope documentation](https://laravel.com/docs/telescope) for the latest compatibility matrix, especially after major Laravel releases. The Telescope team typically releases a compatible version quickly after each new framework version drops.

---

## Telescope vs. Debugbar

**Debugbar** — Quick inline debugging for the current page (web only)  
**Telescope** — Persistent dashboard for *all* activity (APIs, jobs, commands, everything)

Use both: Debugbar for quick checks, Telescope for deep dives.

---

## Start Today

Five minutes to install. Zero configuration required. The payoff is *immediate*, next time something breaks, you open Telescope instead of `dd()` and the answer is there.

If you're building Laravel apps without Telescope, you're making it harder than it needs to be.

---

*Using Telescope in your workflow? Share how in the comments.*
