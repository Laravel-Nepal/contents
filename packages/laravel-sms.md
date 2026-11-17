---
name: Laravel SMS
author_username: mr-rijal
github: mr-rijal/laravel-sms
packagist: mr-rijal/laravel-sms
tags: ["laravel", "sms", "notifications", "whatsapp", "twilio"]
---

One fluent API for sending SMS from [Laravel](https://laravel.com/) through **20 providers**, with failover, queueing, notifications, delivery webhooks and built-in testing.

## Overview

- **20 providers** in one API, plus `failover`, `log` and `fake` drivers
- **Failover:** if one provider fails, only the failed recipients are retried on the next
- **Fast:** bulk endpoints where available, concurrent requests everywhere else
- **Queue-ready:** encrypted jobs, one per recipient
- **Laravel-native:** notification channel, events, webhooks and `Sms::fake()` for tests

Requires PHP 8.2+ and Laravel 11, 12 or 13.

## Installation

```bash
composer require mr-rijal/laravel-sms
php artisan vendor:publish --tag=sms-config
```

Pick a provider and add its credentials to `.env`:

```env
SMS_PROVIDER=twilio
TWILIO_SID=
TWILIO_TOKEN=
TWILIO_FROM=+15550000000
```

Send a test message:

```bash
php artisan sms:test +15551234567
```

## Supported Drivers

| Global | Regional | Utility |
|---|---|---|
| Twilio (`twilio`) | MSG91 (`msg91`), India | Failover (`failover`) |
| Vonage (`vonage`) | Sparrow SMS (`sparrow`), Nepal | Log (`log`), for development |
| Sinch (`sinch`) | Termii (`termii`), Africa | Fake (`fake`), for tests |
| Bird / MessageBird (`bird`) | Sendchamp (`sendchamp`), Africa | |
| Plivo (`plivo`) | Africa's Talking (`africastalking`), Africa | |
| Telnyx (`telnyx`) | Bandwidth (`bandwidth`), US / Canada | |
| Infobip (`infobip`) | | |
| ClickSend (`clicksend`) | | |
| Clickatell (`clickatell`) | | |
| Brevo (`brevo`) | | |
| SendPulse (`sendpulse`) | | |
| BulkSMS (`bulksms`) | | |
| Amazon SNS (`aws_sns`)* | | |
| WhatsApp Cloud API (`whatsapp`) | | |

\* Amazon SNS needs `composer require aws/aws-sdk-php`.

Need another provider? Register your own with `Sms::extend()`.

## Usage

```php
use MrRijal\LaravelSms\Facades\Sms;

// Default provider
Sms::to('+15551234567')->message('Your code is 4821')->send();

// Specific provider, multiple recipients
Sms::provider('sinch')
    ->to(['+15551234567', '+15557654321'])
    ->from('ACME')
    ->message('Maintenance tonight at 10pm')
    ->send();

// Queue it
Sms::to('+15551234567')->message('Hi')->sendLater();
```

Use failover across providers:

```env
SMS_PROVIDER=failover
SMS_FAILOVER_DRIVERS=twilio,vonage,sinch
```

## Notifications

```php
public function via(object $notifiable): array
{
    return ['sms'];
}

public function toSms(object $notifiable): SmsMessage
{
    return SmsMessage::create("Order #{$this->order->id} has shipped.");
}
```

## Testing

```php
Sms::fake();

// ... run your code

Sms::assertSentTo('+15551234567');
```

View the full documentation on [GitHub](https://github.com/mr-rijal/laravel-sms).