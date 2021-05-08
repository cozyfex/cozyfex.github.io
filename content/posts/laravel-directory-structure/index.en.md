---
title: "Laravel Directory Structure"

date: 2021-04-26T15:00:44+09:00

author: CozyFex

categories:

- laravel

tags:

- laravel

keywords:

- laravel

---

{{<admonition note>}}  
This document is for Laravel 8.x version and comes from official site [Laravel](https://laravel.com/docs/8.x/structure)
.  
{{</admonition>}}

## `app`

> The `app` directory contains the core code of your application.
> ### `app/Broadcasting`
>> The `Boardcasting` directory contains all of the broadcast channel classes for your application.
> These classes are generated using the `make:channel` command.
> This directory does not exist by default, but will be created for you when you create your first channel.
> ### `app/Console`
>> The `Console` directory contains all of the custom Artisan commands for your application.
> These commands may be generated using the `make:command` command.
> ### `app/Events`
>> This directory does not exist by default, but will be created for you by the `event:generate` and `make:event` Artisan commands.
> The `Events` directory houses event classes.
> ### `app/Exceptions`
>> The `Exceptions` directory contains your application's exception handler and is also a good place to place any exceptions thrown by your application.
> ### `app/Http`
>> The `Http` directory contains your controllers, middleware, and form requests.
> ### `app/Jobs`
>> This directory does not exist by default, but will be created for you if you execute the `make:job` Artisan command.
> The `Jobs` directory houses the queueable jobs for your applications.
> ### `app/Listeners`
>> This directory does not exist by default, but will be created for you if you execute the `event:generate` or `make:listener` Artisan commands.
> The `Listeners` directory contains the classes that handle your events.
> ### `app/Mail`
>> This directory does not exist by default, but will be created for you if you execute the `make:mail` Artisan command.
> ### `app/Models`
>> The `Models` directory contains all of your Eloquent model classes.
> The Eloquent ORM included with Laravel provides a beautiful, simple ActiveRecord implementation for working with your database.
> ### `app/Notifications`
>> This directory does not exist by default, but will be created for you if you execute the `make:notification` Artisan command.
> It can be email, Slack, SMS, or stored in a database.
> ### `app/Polices`
>> This directory does not exist by default, but will be created for you if you execute the `make:policy` Artisan command.
> The `Policies` directory contains the authorization policy classes for your application.
> ### `app/Providers`
>> The `Providers` directory contains all of the service providers for your application.
> ### `app/Rules`
>> This directory does not exist by default, but will be created for you if you execute the `make:rule` Artisan command.
> The `Rules` directory contains the custom validation rule objects for your application.

## `bootstrap`

> The `bootstrap` directory contains `app.php` file which bootstraps framework.  
> The directory also houses a `cache` directory which contains framework generated files.

## `config`

> The `config` directory, as the name implies, contains all of your application's configuration files.

## `database`

> The `database` directory contains your database migrations, model factories, and seeds.

## `public`

> The `public` directory contains the `index.php` file, which is the entry point for all requests entering your application and configures autoloading. This direcotry also houses your assets such as images, JavaScript, and CSS.

## `resources`

> The `resources` directory contains your views as well as your raw, un-compiled assets such as CSS or JavaScript. This directory also houses all of your language files.

## `routes`

> The `routes` directory contains all of the route definitions for your applications.
> By default, several route files are included with Laravel: `web.php`, `api.php`, `console.php`, and `channels.php`.
> ### `routes/web.php`
>> The `web.php` file contains routes that the `RouteServiceProvider` places in the `web` middleware group, which provides session state, CSRF protection, and cookie encryption.
> If your application does not offer a stateless, RESTful API then it is likely that all of your routes will most likely be defined in the `web.php` file.
> ### `routes/api.php`
>> The `api.php` file contains routes that the `RouteServiceProvider` places in the `api` middleware group.
> These routes are intended to be stateless, so requests entering the application through these routes are intended to be authenticated via token and will not have access to session state.  
> `api.php` 파일은 `api` 미들웨어 그룹에 위치한 `RouteServiceProvider`의 경로를 포함한다.
> ### `routes/console.php`
>> The `console.php` file is all of your closure based console commands.
> ### `routes/channels.php`
>> The `channels.php` file is where you may register all of the event broadcasting channels that your application supports.

## `storage`

> The `storage` directory contains your logs, compiled Blade templates, file based sessions, file caches, and other files generated by the framework.
> ### `storage/app`
>> Application generated files.
> ### `storage/framework`
>> Framework generated files and caches.
> ### `storage/logs`
>> Application's log files.

## `tests`

> The `tests` directory contains your automated tests.

## `vendor`

> The `vender` directory contains your `Composer` dependencies.  
