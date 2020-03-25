# Laravel User Login log

## Feature

- [x] Log user login records
- [ ] Analyse user login logs

## Installation

### Required

- PHP 7.0+
- Laravel 5.5+

You can install the package using composer

```php
composer require moecasts/laravel-user-login-log
```

If you are using Laravel < 5.5, you need to add provider to your config/app.php providers array:

```php
Moecasts\Laravel\UserLoginLog\UserLoginLogServiceProvider,
```

Publish the mirgrations file:

```bash
php artisan vendor:publish --tag=laravel-user-login-log-migrations
```

As optional if you want to modify the default configuration, you can publish the configuration file:

```bash
php artisan vendor:publish --tag=laravel-user-login-log-config
```

And create tables:

```bash
php artisan migrate
```

## Configurations

```php
return [
    /**
     * cache avtive time (seconds)
     */
    'expire' => 300,
];
```

## Usage

Firstly, add `LoginLoggable` trait to your authenticatable model.

```php
use Moecasts\Laravel\UserLoginLog\Traits\LoginLoggable;

class User extends Authenticatable
{
    use LoginLoggable;
}
```

Next, simply register the newly created class after auth middleware in your middleware stack.

```php
// app/Http/Kernel.php

class Kernel extends HttpKernel
{
    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        // ...
        'login.log' => \Moecasts\Laravel\UserLoginLog\Middleware\UserLoginLogMiddleware::class,
    ];
    
    // ...
}
```

Finally, use the middleware:

```php
Route::get('hello')->middleware(['auth', 'login.log']);
```

### Methods

#### Get user's logs

```php
$user = new User;
$user->loginLogs;
```

#### Create user login log

```php
$user = new User;
$user->createLoginLog();
```

#### Log when user newly login

This function is depet on cache, when your newly login, it will set a cache with for `$seconds` or default config ( `loginlogs.expire` ) seconds when `$seconds`  is not set.

```php
$user = new User;
// $user->logLogin($seconds = null)
$user->logLogin();
```

#### Check is new login

```php
$user = new User;
$user->isNewLogin();
```

### Let's enjoy coding!