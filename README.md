# Laravel Sudo

Composer package for Laravel 5.0 and above that allows for the use of "sudo mode" in protected areas. "Sudo mode" refers to the requirement that a user must re-enter his password before performing certain actions and will not have to re-enter it for a certain amount of time, similar to how the [sudo utility](https://en.wikipedia.org/wiki/Sudo) functions on *nix-based operating systems.

The mode will last for a pre-determined amount of time before either a re-prompt or the currently-authenticated user logs-out.

This package can also integrate with the [Laravel Directory Authentication](https://github.com/csun-metalab/laravel-directory-authentication) package if the configured `User` model is an instance or subclass of the `CSUNMetaLab\Authentication\MetaUser` class.

The aforementioned integration would check whether the current user is actually masquerading as someone else. That way, the password prompt will be for the *masquerading* user, not the *masqueraded* user.

## Table of Contents

* [Installation](#installation)
    * [Composer and Service Provider](#composer-and-service-provider)
    * [Middleware Installation](#middleware-installation)
    * [Publish Everything](#publish-everything)
* [Required Environment Variables](#required-environment-variables)
* [Optional Environment Variables](#optional-environment-variables)
* [Middleware](#middleware)
    * [Enforcing Sudo Mode](#enforcing-sudo-mode)
    * [Sudo Middleware](#sudo-middleware)
* [Custom Messages](#custom-messages)
    * [Validation Messages](#validation-messages)
    * [Authentication Messages](#authentication-messages)
* [View](#view)
* [Resources](#resources)

## Installation

### Composer and Service Provider

#### Composer

To install from Composer, use the following command:

```
composer require csun-metalab/laravel-sudo
```

#### Service Provider

Add the service provider to your `providers` array in `config/app.php` in Laravel as follows:

```
'providers' => [
   //...

   CSUNMetaLab\Sudo\Providers\SudoServiceProvider::class,

   // You can also use this based on Laravel convention:
   // 'CSUNMetaLab\Sudo\Providers\SudoServiceProvider',

   //...
],
```

### Middleware Installation

Add the middleware to your `$routeMiddleware` array in `app/Http/Kernel.php` to enable it to protect routes:

```
protected $routeMiddleware = [
   //...

   'sudo' => \CSUNMetaLab\Sudo\Http\Middleware\Sudo::class,

   // You can also use this based on Laravel convention:
   // 'sudo' => 'CSUNMetaLab\Sudo\Http\Middleware\Sudo',

   //...
];
```

### Publish Everything

Finally, run the following Artisan command to publish everything:

```
php artisan vendor:publish
```

The following assets are published:

* Configuration (tagged as `config`) - these go into your `config` directory
* Messages (tagged as `lang`) - these go into your `resources/lang/en` directory as `sudo.php`
* Views (tagged as `views`) - these go into your `resources/views/vendor/sudo` directory

## Required Environment Variables

There are currently no required environment variables but there are [optional environment variables](#optional-environment-variables).

## Optional Environment Variables

### SUDO_DURATION

The time (in minutes) that "sudo mode" is active for the existing authenticated user before a reprompt. This time will reset if he logs-out before the duration has been exhausted.

Default is 120 minutes (two hours).

### SUDO_USERNAME

The attribute in your configured `User` model that represents the username by which an individual authenticates.

Default is `email`.

## Middleware

This package is driven primarily by a single middleware class though it contains a considerable amount of functionality and decision-making.

### Enforcing Sudo Mode

In order to enforce "sudo mode" you would need to protect a set of routes with both the `auth` and `sudo` middleware as shown here:

```
Route::group(['middleware' => ['auth', 'sudo']], function () {
  Route::get('secret', 'SomeController@getSecret');
  Route::post('secret', 'SomeController@postSecret');
  Route::get('admin', 'SomeController@getAdmin');
  Route::post('admin', 'SomeController@postAdmin');
});
```

This is just an example, of course, but the above route group would first ensure that the individual is authenticated before attempting to access the sections. If the individual is authenticated, they would then be greeted with a password re-prompt if "sudo mode" is not currently active based upon the criteria set forth in the [Sudo Middleware](#sudo-middleware) section.

### Sudo Middleware

TBD

## Custom Messages

The custom messages for this package can be found in `resources/lang/en/sudo.php` by default. The messages can also be overridden as needed.

You may also translate the messages in that file to other languages to promote localization, as well.

The package reads from this file (using the configured localization) for all messages it must display to the user or write to any logs.

### Validation Messages

* `errors.v.password.required` - error message raised when the `sudo_password` field is left empty on the view

### Authentication Messages

* `errors.a.password.invalid` - error message raised when the sudo password entered does not match the user's credentials

## View

TBD

## Resources

### Middleware

* [Middleware in Laravel 5.0](https://laravel.com/docs/5.0/middleware)
* [Middleware in Laravel 5.1](https://laravel.com/docs/5.1/middleware)
* [Middleware in Laravel 5.2](https://laravel.com/docs/5.2/middleware)
* [Middleware in Laravel 5.3](https://laravel.com/docs/5.3/middleware)
* [Middleware in Laravel 5.4](https://laravel.com/docs/5.4/middleware)
* [Middleware in Laravel 5.5](https://laravel.com/docs/5.5/middleware)