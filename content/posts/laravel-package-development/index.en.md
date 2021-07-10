---
title: "Laravel Package Development"

date: 2021-05-22T14:08:57+09:00

author: CozyFex

categories:

- php
- laravel
- composer
- package

tags:

- php
- laravel
- composer
- package
- packagist
- github

keywords:

- php
- laravel
- composer
- package
- packagist
- github

---

## Required Knowledge

### Composer

{{<admonition note Composer true>}}  
`Composer` is a tool for dependency management in PHP.  
{{</admonition>}}

### Packagist

{{<admonition note Packagist true>}}  
`Packagist` is the default `Composer` package repository.  
{{</admonition>}}

### Laravel

{{<admonition note Laravel true>}}  
`Laravel` is a web application framework in PHP.  
{{</admonition>}}

### GitHub

{{<admonition note GitHub true>}}  
`GitHub` is a code hosting platform for version control and collaboration.  
{{</admonition>}}

## Install Laravel

{{<admonition note Note true>}}  
I don't explain how to set environment up for `PHP` and `Composer` in this post.  
{{</admonition>}}

```shell
composer create-project laravel/laravel my-project
```

## Settings for Package Development

### Directory Structure

{{<admonition note "Directory Structure" true>}}  
`<Laravel Project>`/`<Packages Direcotry>`/`<Vendor Name>`/`<Package Name>`/src  
{{</admonition>}}

#### Create a Packages Directory

```shell
cd my-project
mkdir packages
```

#### Create a Vendor Directory

```shell
cd packages
mkdir cozyfex
```

#### Create a Package Directory

```shell
cd cozyfex
mkdir my-package
```

### Create a Source Directory

```shell
cd my-package
mkdir src
```

### Initialize `git`

#### GitHub Initialization

- Go to `GitHub` and register
- Create a repository `laravel-my-package`

#### `git` Local Initialization

```shell
# my-project/packages/cozyfex/my-package

# Init
git init

# Setting ignore
vi .gitignore

# Add remote repository, below is example for this post.
git remote add origin git@github.com:cozyfex/laravel-my-package.git
```

#### Check Sync Local and GitHub

##### Push to Repository

```shell
git add -A
git commit -m "First commit"
git push -u origin master
```

##### Check GitHub Repository

- Go to your GitHub repository and check!

### Initialize `composer`

#### Using `composer`

```shell
# my-project/packages/cozyfex/my-package
composer init
```

#### Using Template

```shell
# my-project/packages/cozyfex/my-package
vi composer.json
```

<sub>composer.json</sub>

```json
{
  "name": "cozyfex/my-package",
  "description": "Laravel Package Development",
  "keywords": [
    "laravel",
    "package",
    "development"
  ],
  "license": "MIT",
  "support": {
    "issues": "https://github.com/cozyfex/laravel-my-package/issues",
    "source": "https://github.com/cozyfex/laravel-my-package"
  },
  "authors": [
    {
      "name": "Felix D.H. Kang",
      "email": "cozyfex@gmail.com"
    }
  ],
  "require": {
    "php": "^7.3|^8.0",
    "laravel/framework": "^8.0.0"
  },
  "autoload": {
    "psr-4": {
      "CozyFex\\MyPackage\\": "src/"
    }
  },
  "extra": {
    "laravel": {
      "providers": [
        "CozyFex\\MyPackage\\PackageServiceProvider"
      ]
    }
  },
  "minimum-stability": "dev"
}
```

#### Core Attributes

{{<admonition note Note true>}}  
If you want to more detail about attributes of `composer.json`, go to official [Composer Document](https://getcomposer.org/).  
{{</admonition>}}

##### `name`

{{<admonition note name true>}}  
`<vendor_name>`/`<package_name>`
{{</admonition>}}

##### `support`

{{<admonition note support true>}}  
This is for supporting urls.  
{{</admonition>}}

##### `authors`

{{<admonition note authors true>}}  
This is information of `authors`.  
{{</admonition>}}

##### `require`

{{<admonition note require true>}}  
This is main attribute of `composer`.  
The information is to solve dependency of a project or a library.  
This package is depended `laravel/framework`.  
{{</admonition>}}

##### `autoload`

{{<admonition note autoload true>}}  
This is setting for autoload the package sources.  
{{</admonition>}}

##### `extra`

{{<admonition note extra true>}}  
This is for providing package provider to `laravel`.  
We will create a `PackageServiceProvider` file in this post later.  
{{</admonition>}}

## Create a Package Sources

### Create a Provider

```shell
# my-project
php artisan make:provider PackageServiceProvider
mv app/Providers/PackageServiceProvider.php packages/cozyfex/my-package/src/
```

### Create Views

#### Create a Directory

```shell
# my-project/packages/cozyfex/my-package/src
mkdir views

cd views
# my-project/packages/cozyfex/my-package/src/views
mkdir MyPackage
```

#### Create View Files

<sub>exist_session.blade.php</sub>

```html
<!DOCTYPE html>
<html>
<head>
    <title>Exist Session</title>
</head>
<body>
<p>Exist Session</p>
</body>
</html>
```

<sub>none_session.blade.php</sub>

```html
<!DOCTYPE html>
<html>
<head>
    <title>None Session</title>
</head>
<body>
<p>None Session</p>
</body>
</html>
```

#### View Settings in Provider

<sub>my-project/packages/cozyfex/my-package/src/PackageServiceProvider.php</sub>

```php
<?php

namespace CozyFex\MyPackage;

use Illuminate\Support\ServiceProvider;

class PackageServiceProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        # Here!
        # The 'my-package' is a namespace.
        $this->loadViewsFrom(__DIR__.'/views', 'my-package');
    }
}
```

### Create Controllers

#### Create a Directory

```shell
# my-project/packages/cozyfex/my-package/src
mkdir Controllers
```

#### Exist Session Controller

```shell
# my-project
php artisan make:controller -r ExistSessionController
mv app/Http/Controllers/ExistSessionController.php packages/cozyfex/my-package/src/Controllers/
```

<sub>ExistSessionController.php</sub>

```php
<?php

namespace CozyFex\MyPackage\Controllers;

use Illuminate\Http\Request;

use App\Http\Controllers\Controller;

class ExistSessionController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        return response()->view('my-package::MyPackage.exist_session');
    }

    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        //
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     *
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        //
    }

    /**
     * Display the specified resource.
     *
     * @param  int  $id
     *
     * @return \Illuminate\Http\Response
     */
    public function show($id)
    {
        //
    }

    /**
     * Show the form for editing the specified resource.
     *
     * @param  int  $id
     *
     * @return \Illuminate\Http\Response
     */
    public function edit($id)
    {
        //
    }

    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     *
     * @return \Illuminate\Http\Response
     */
    public function update(Request $request, $id)
    {
        //
    }

    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     *
     * @return \Illuminate\Http\Response
     */
    public function destroy($id)
    {
        //
    }
}
```

#### None Session Controller

```shell
# my-project
php artisan make:controller -r NoneSessionController
mv app/Http/Controllers/NoneSessionController.php packages/cozyfex/my-package/src/Controllers/
```

<sub>NoneSessionController.php</sub>

```php
<?php

namespace CozyFex\MyPackage\Controllers;

use Illuminate\Http\Request;

use App\Http\Controllers\Controller;

class NoneSessionController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        return response()->view('my-package::MyPackage.none_session');
    }

    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        //
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     *
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        //
    }

    /**
     * Display the specified resource.
     *
     * @param  int  $id
     *
     * @return \Illuminate\Http\Response
     */
    public function show($id)
    {
        //
    }

    /**
     * Show the form for editing the specified resource.
     *
     * @param  int  $id
     *
     * @return \Illuminate\Http\Response
     */
    public function edit($id)
    {
        //
    }

    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     *
     * @return \Illuminate\Http\Response
     */
    public function update(Request $request, $id)
    {
        //
    }

    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     *
     * @return \Illuminate\Http\Response
     */
    public function destroy($id)
    {
        //
    }
}
```

### Create Routes

#### Create a `routes.php`

```shell
# my-project/packages/cozyfex/my-package/src
vi routes.php
```

<sub>routes.php</sub>

```php
<?php

use Illuminate\Support\Facades\Route;

use CozyFex\MyPackage\Controllers\ExistSessionController;
use CozyFex\MyPackage\Controllers\NoneSessionController;

Route::middleware(['web', 'auth'])->resource('exist', ExistSessionController::class);
Route::middleware(['web'])->resource('none', NoneSessionController::class);
```

{{<admonition note Middleware true>}}  
At this time, I want to tell you about `middleware`.  
Above the source, there are two `middleware`.  
The names are `web` and `auth`.  
The `web` is to access web session.(The session is starting in `web` middleware.)  
If you don't use `web`, you cannot access the session in your package.  
That means, if you use only the `auth` middleware without the `web`, the `auth` middleware is not going to work.

If you want to know more details about `Laravel` `middleware`, go to the [Official Document](https://laravel.com/docs/8.x/middleware)  
{{</admonition>}}

#### Routes Settings in Provider

<sub>my-project/packages/cozyfex/my-package/src/PackageServiceProvider.php</sub>

```php
<?php

namespace CozyFex\MyPackage;

use Illuminate\Support\ServiceProvider;

class PackageServiceProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/views', 'my-package');
        # Here!
        $this->loadRoutesFrom(__DIR__.'/routes.php');
    }
}
```

## Connection Laravel and Package

### Register Autoload to Laravel `composer.json`

<sub>my-project/composer.json</sub>

{{<admonition note "Check!" true>}}  
`"CozyFex\\MyPackage\\": "packages/cozyfex/my-package/src"`
{{</admonition>}}

```json
{
  "name": "laravel/laravel",
  "type": "project",
  "description": "The Laravel Framework.",
  "keywords": [
    "framework",
    "laravel"
  ],
  "license": "MIT",
  "require": {
    "php": "^7.3|^8.0",
    "fideloper/proxy": "^4.4",
    "fruitcake/laravel-cors": "^2.0",
    "guzzlehttp/guzzle": "^7.0.1",
    "laravel/framework": "^8.40",
    "laravel/tinker": "^2.5"
  },
  "require-dev": {
    "facade/ignition": "^2.5",
    "fakerphp/faker": "^1.9.1",
    "laravel/sail": "^1.0.1",
    "mockery/mockery": "^1.4.2",
    "nunomaduro/collision": "^5.0",
    "phpunit/phpunit": "^9.3.3"
  },
  "autoload": {
    "psr-4": {
      "App\\": "app/",
      "Database\\Factories\\": "database/factories/",
      "Database\\Seeders\\": "database/seeders/",
      "CozyFex\\MyPackage\\": "packages/cozyfex/my-package/src"
    }
  },
  "autoload-dev": {
    "psr-4": {
      "Tests\\": "tests/"
    }
  },
  "scripts": {
    "post-autoload-dump": [
      "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
      "@php artisan package:discover --ansi"
    ],
    "post-root-package-install": [
      "@php -r \"file_exists('.env') || copy('.env.example', '.env');\""
    ],
    "post-create-project-cmd": [
      "@php artisan key:generate --ansi"
    ]
  },
  "extra": {
    "laravel": {
      "dont-discover": []
    }
  },
  "config": {
    "optimize-autoloader": true,
    "preferred-install": "dist",
    "sort-packages": true
  },
  "minimum-stability": "dev",
  "prefer-stable": true
}
```

### Generate Vendor Autoload

{{<admonition note "IMPORTANT!" true>}}  
This is very IMPORTANT!!  
DON'T MISS IT!!
{{</admonition>}}

```shell
# my-project
composer dump-autoload
```

### Add Provider to Laravel Providers

{{<admonition note Provider true>}}  
`CozyFex\MyPackage\PackageServiceProvider::class`
{{</admonition>}}

<sub>my-project/config/app.php</sub>

```php
<?php

return [

    /*
    |--------------------------------------------------------------------------
    | Application Name
    |--------------------------------------------------------------------------
    |
    | This value is the name of your application. This value is used when the
    | framework needs to place the application's name in a notification or
    | any other location as required by the application or its packages.
    |
    */

    'name' => env('APP_NAME', 'Laravel'),

    /*
    |--------------------------------------------------------------------------
    | Application Environment
    |--------------------------------------------------------------------------
    |
    | This value determines the "environment" your application is currently
    | running in. This may determine how you prefer to configure various
    | services the application utilizes. Set this in your ".env" file.
    |
    */

    'env' => env('APP_ENV', 'production'),

    /*
    |--------------------------------------------------------------------------
    | Application Debug Mode
    |--------------------------------------------------------------------------
    |
    | When your application is in debug mode, detailed error messages with
    | stack traces will be shown on every error that occurs within your
    | application. If disabled, a simple generic error page is shown.
    |
    */

    'debug' => (bool) env('APP_DEBUG', false),

    /*
    |--------------------------------------------------------------------------
    | Application URL
    |--------------------------------------------------------------------------
    |
    | This URL is used by the console to properly generate URLs when using
    | the Artisan command line tool. You should set this to the root of
    | your application so that it is used when running Artisan tasks.
    |
    */

    'url' => env('APP_URL', 'http://localhost'),

    'asset_url' => env('ASSET_URL', null),

    /*
    |--------------------------------------------------------------------------
    | Application Timezone
    |--------------------------------------------------------------------------
    |
    | Here you may specify the default timezone for your application, which
    | will be used by the PHP date and date-time functions. We have gone
    | ahead and set this to a sensible default for you out of the box.
    |
    */

    'timezone' => 'UTC',

    /*
    |--------------------------------------------------------------------------
    | Application Locale Configuration
    |--------------------------------------------------------------------------
    |
    | The application locale determines the default locale that will be used
    | by the translation service provider. You are free to set this value
    | to any of the locales which will be supported by the application.
    |
    */

    'locale' => 'en',

    /*
    |--------------------------------------------------------------------------
    | Application Fallback Locale
    |--------------------------------------------------------------------------
    |
    | The fallback locale determines the locale to use when the current one
    | is not available. You may change the value to correspond to any of
    | the language folders that are provided through your application.
    |
    */

    'fallback_locale' => 'en',

    /*
    |--------------------------------------------------------------------------
    | Faker Locale
    |--------------------------------------------------------------------------
    |
    | This locale will be used by the Faker PHP library when generating fake
    | data for your database seeds. For example, this will be used to get
    | localized telephone numbers, street address information and more.
    |
    */

    'faker_locale' => 'en_US',

    /*
    |--------------------------------------------------------------------------
    | Encryption Key
    |--------------------------------------------------------------------------
    |
    | This key is used by the Illuminate encrypter service and should be set
    | to a random, 32 character string, otherwise these encrypted strings
    | will not be safe. Please do this before deploying an application!
    |
    */

    'key' => env('APP_KEY'),

    'cipher' => 'AES-256-CBC',

    /*
    |--------------------------------------------------------------------------
    | Autoloaded Service Providers
    |--------------------------------------------------------------------------
    |
    | The service providers listed here will be automatically loaded on the
    | request to your application. Feel free to add your own services to
    | this array to grant expanded functionality to your applications.
    |
    */

    'providers' => [

        /*
         * Laravel Framework Service Providers...
         */
        Illuminate\Auth\AuthServiceProvider::class,
        Illuminate\Broadcasting\BroadcastServiceProvider::class,
        Illuminate\Bus\BusServiceProvider::class,
        Illuminate\Cache\CacheServiceProvider::class,
        Illuminate\Foundation\Providers\ConsoleSupportServiceProvider::class,
        Illuminate\Cookie\CookieServiceProvider::class,
        Illuminate\Database\DatabaseServiceProvider::class,
        Illuminate\Encryption\EncryptionServiceProvider::class,
        Illuminate\Filesystem\FilesystemServiceProvider::class,
        Illuminate\Foundation\Providers\FoundationServiceProvider::class,
        Illuminate\Hashing\HashServiceProvider::class,
        Illuminate\Mail\MailServiceProvider::class,
        Illuminate\Notifications\NotificationServiceProvider::class,
        Illuminate\Pagination\PaginationServiceProvider::class,
        Illuminate\Pipeline\PipelineServiceProvider::class,
        Illuminate\Queue\QueueServiceProvider::class,
        Illuminate\Redis\RedisServiceProvider::class,
        Illuminate\Auth\Passwords\PasswordResetServiceProvider::class,
        Illuminate\Session\SessionServiceProvider::class,
        Illuminate\Translation\TranslationServiceProvider::class,
        Illuminate\Validation\ValidationServiceProvider::class,
        Illuminate\View\ViewServiceProvider::class,

        /*
         * Package Service Providers...
         */
        
        # Here!
        CozyFex\MyPackage\PackageServiceProvider::class,

        /*
         * Application Service Providers...
         */
        App\Providers\AppServiceProvider::class,
        App\Providers\AuthServiceProvider::class,
        // App\Providers\BroadcastServiceProvider::class,
        App\Providers\EventServiceProvider::class,
        App\Providers\RouteServiceProvider::class,

    ],

    /*
    |--------------------------------------------------------------------------
    | Class Aliases
    |--------------------------------------------------------------------------
    |
    | This array of class aliases will be registered when this application
    | is started. However, feel free to register as many as you wish as
    | the aliases are "lazy" loaded so they don't hinder performance.
    |
    */

    'aliases' => [

        'App'          => Illuminate\Support\Facades\App::class,
        'Arr'          => Illuminate\Support\Arr::class,
        'Artisan'      => Illuminate\Support\Facades\Artisan::class,
        'Auth'         => Illuminate\Support\Facades\Auth::class,
        'Blade'        => Illuminate\Support\Facades\Blade::class,
        'Broadcast'    => Illuminate\Support\Facades\Broadcast::class,
        'Bus'          => Illuminate\Support\Facades\Bus::class,
        'Cache'        => Illuminate\Support\Facades\Cache::class,
        'Config'       => Illuminate\Support\Facades\Config::class,
        'Cookie'       => Illuminate\Support\Facades\Cookie::class,
        'Crypt'        => Illuminate\Support\Facades\Crypt::class,
        'Date'         => Illuminate\Support\Facades\Date::class,
        'DB'           => Illuminate\Support\Facades\DB::class,
        'Eloquent'     => Illuminate\Database\Eloquent\Model::class,
        'Event'        => Illuminate\Support\Facades\Event::class,
        'File'         => Illuminate\Support\Facades\File::class,
        'Gate'         => Illuminate\Support\Facades\Gate::class,
        'Hash'         => Illuminate\Support\Facades\Hash::class,
        'Http'         => Illuminate\Support\Facades\Http::class,
        'Lang'         => Illuminate\Support\Facades\Lang::class,
        'Log'          => Illuminate\Support\Facades\Log::class,
        'Mail'         => Illuminate\Support\Facades\Mail::class,
        'Notification' => Illuminate\Support\Facades\Notification::class,
        'Password'     => Illuminate\Support\Facades\Password::class,
        'Queue'        => Illuminate\Support\Facades\Queue::class,
        'Redirect'     => Illuminate\Support\Facades\Redirect::class,
        // 'Redis' => Illuminate\Support\Facades\Redis::class,
        'Request'      => Illuminate\Support\Facades\Request::class,
        'Response'     => Illuminate\Support\Facades\Response::class,
        'Route'        => Illuminate\Support\Facades\Route::class,
        'Schema'       => Illuminate\Support\Facades\Schema::class,
        'Session'      => Illuminate\Support\Facades\Session::class,
        'Storage'      => Illuminate\Support\Facades\Storage::class,
        'Str'          => Illuminate\Support\Str::class,
        'URL'          => Illuminate\Support\Facades\URL::class,
        'Validator'    => Illuminate\Support\Facades\Validator::class,
        'View'         => Illuminate\Support\Facades\View::class,

    ],

];
```

## Interim Check

{{<admonition note Links true>}}  
Run `php artisan serve`

You can check below links now.

`http://localhost:8000/none`  
This link works good.

`http://localhost:8000/exist`  
This link is not going to work as well.  
Don't worry, it was predictable.  
Because we don't install any login process.  
If you want to check all, run below commands in the project directory.  
`composer require laravel/breeze`  
`php artisan breeze:install`  
`npm install && npm run dev`  
{{</admonition>}}

## Additional Features

### Migration

{{<admonition note Migration true>}}  
Before into this section, make sure database connection in `Laravel`.  
{{</admonition>}}

#### Create a `migrations` Directory

```shell
# my-project/packages/cozyfex/my-package/src
mkdir migrations
```

#### Generate Migration File

```shell
# my-project
php artisan make:migration --create=boards create_board
mv database/migrations/2021_05_22_080925_create_board.php packages/cozyfex/my-package/src/migrations/

# If you want to change table scheme, '--create' to '--table'
php artisan make:migration --table=boards add_feild_to_board
```

<sub>my-project/packages/cozyfex/my-package/src/migrations/2021_05_22_080925_create_board.php</sub>

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateBoard extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('boards', function (Blueprint $table) {
            $table->id();
            $table->foreignId('user_id')->constrained();
            $table->string('author');
            $table->string('title');
            $table->text('content');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('boards');
    }
}
```

#### Migrations Settings in Provider

<sub>my-project/packages/cozyfex/my-package/src/PackageServiceProvider.php</sub>

```php
<?php

namespace CozyFex\MyPackage;

use Illuminate\Support\ServiceProvider;

class PackageServiceProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/views', 'my-package');
        $this->loadRoutesFrom(__DIR__.'/routes.php');
        # Here!
        $this->loadMigrationsFrom(__DIR__.'/migrations');
    }
}
```

### Model

#### Create a `Models` Directory

```shell
# my-project/packages/cozyfex/my-package/src
mkdir Models
```

#### Generate a Model

```shell
# my-project
php artisan make:model Board
mv app/Models/Board.php packages/cozyfex/my-package/src/Models/
```

<sub>my-project/packages/cozyfex/my-package/src/Models/Board.php</sub>

```php
<?php

namespace CozyFex\MyPackage\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

use App\Models\User as AppUser;

class Board extends Model
{
    use HasFactory;

    /**
     * @return BelongsTo
     */
    public function users(): BelongsTo
    {
        return $this->belongsTo(AppUser::class);
    }
}
```

### Command

#### Create a `Commands` Directory

```shell
# my-project/packages/cozyfex/my-package/src
mkdir Commands
```

#### Generate a Command

```shell
# my-project
php artisan make:command InstallCommand
mv app/Console/Commands/InstallCommand.php packages/cozyfex/my-package/src/Commands
```

<sub>my-project/packages/cozyfex/my-package/src/Commands/InstallCommand.php</sub>

```php
<?php

namespace CozyFex\MyPackage\Commands;

use Illuminate\Console\Command;

class InstallCommand extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'cozyfex:my-package:install';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Command description';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return int
     */
    public function handle()
    {
        $this->call('migrate');

        $this->info('[CozyFex MyPackage] was installed successfully!');

        return 0;
    }
}
```

#### Command Settings in Provider

<sub>my-project/packages/cozyfex/my-package/src/PackageServiceProvider.php</sub>

```php
<?php

namespace CozyFex\MyPackage;

use Illuminate\Support\ServiceProvider;

use CozyFex\MyPackage\Commands\InstallCommand;

class PackageServiceProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        $this->loadViewsFrom(__DIR__.'/views', 'my-package');
        $this->loadRoutesFrom(__DIR__.'/routes.php');
        $this->loadMigrationsFrom(__DIR__.'/migrations');

        # Here!
        if ($this->app->runningInConsole()) {
            $this->commands([
                InstallCommand::class,
            ]);
        }
    }
}
```

#### Check Command

```shell
# my-project
php artisan

# Only cozyfex
php artisan | grep cozyfex
```

### Publish PackageServiceProvider

{{<admonition note "Publish in Laravel" true>}}  
I don't guarantee this package is perfect fit to your project.  
Some of them want to use this package, but they also want to change a part of the package.  
Reason why the `Laravel` provides a function for that in `Provider`.  
{{</admonition>}}

<sub>my-project/packages/cozyfex/my-package/src/PackageServiceProvider.php</sub>

```php
<?php

namespace CozyFex\MyPackage;

use Illuminate\Support\ServiceProvider;

use CozyFex\MyPackage\Commands\InstallCommand;

class PackageServiceProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap services.
     *
     * @return void
     */
    public function boot()
    {
        # Load View
        $this->loadViewsFrom(__DIR__.'/views', 'my-package');

        # Load Route
        $this->loadRoutesFrom(__DIR__.'/routes.php');

        # Load Migration
        $this->loadMigrationsFrom(__DIR__.'/migrations');

        # Here!
        # Publish View
        $this->publishes([
            __DIR__.'/views' => resource_path('views/CozyFex'),
        ]);

        # Commands
        if ($this->app->runningInConsole()) {
            $this->commands([
                InstallCommand::class,
            ]);
        }
    }
}
```

#### Laravel Publish Path Functions

##### `resource_path`

{{<admonition note resource_path true>}}  
`my-project/resources`  
{{</admonition>}}

##### `config_path`

{{<admonition note config_path true>}}  
`my-project/config`  
{{</admonition>}}

##### `public_path`

{{<admonition note public_path true>}}  
`my-project/public`  
{{</admonition>}}

##### `database_path`

{{<admonition note database_path true>}}  
`my-project/database`  
{{</admonition>}}

#### Publish Command

```shell
php artisan vendor:publish
```

```shell
Which provider or tag's files would you like to publish?:
  [0 ] Publish files from all providers and tags listed below
  [1 ] Provider: CozyFex\MyPackage\PackageServiceProvider
  [2 ] Provider: Facade\Ignition\IgnitionServiceProvider
  [3 ] Provider: Fideloper\Proxy\TrustedProxyServiceProvider
  [4 ] Provider: Fruitcake\Cors\CorsServiceProvider
  [5 ] Provider: Illuminate\Foundation\Providers\FoundationServiceProvider
  [6 ] Provider: Illuminate\Mail\MailServiceProvider
  [7 ] Provider: Illuminate\Notifications\NotificationServiceProvider
  [8 ] Provider: Illuminate\Pagination\PaginationServiceProvider
  [9 ] Provider: Laravel\Sail\SailServiceProvider
  [10] Provider: Laravel\Tinker\TinkerServiceProvider
  [11] Tag: cors
  [12] Tag: flare-config
  [13] Tag: ignition-config
  [14] Tag: laravel-errors
  [15] Tag: laravel-mail
  [16] Tag: laravel-notifications
  [17] Tag: laravel-pagination
  [18] Tag: sail
> 1
```

#### Check Published Directory

```shell
cd my-project/resources/views
```

## Push Package to GitHub

### Commit

```shell
git status
git add -A
git commit -m "My package"
git push origin master
```

### Tagging

{{<admonition note "For Packagist Versioning" true>}}  
This tag is used for versioning of Packagist.  
{{</admonition>}}

```shell
git tag v1.0.0
```

### Push Tag

```shell
git push origin v1.0.0
```

## Register Package to `packagist.org`

{{<admonition note Recommendation true>}}  
I recommend to sign up with your GitHub account.  
This is for register a `web hook` in GitHub by `packagist.org`.  
Of course, you need to agree with it.  
{{</admonition>}}

{{<mermaid>}}  
graph TD;  
L(Login 'packagist.org') --> S(Go to 'Submit' menu on right top side)  
S --> C(Copy and Paste GitHub repository URL to input box that is above 'Check' button)  
C --> CC(Click 'Check' button)  
CC --> M(Click `Submit` button)  
{{</mermaid>}}

## Check Package on New Laravel Project!

### Generate New Laravel Project

```shell
composer create-project laravel/laravel new-project
```

### Composer Require MyPackage

```shell
# new-project
cd new-project
composer require cozyfex/my-package
```

### Run and Check Links Again

```shell
php artisan serve
```

{{<admonition note Links true>}}  
[http://localhost:8000/none](http://localhost:8000/none)  
[http://localhost:8000/exist](http://localhost:8000/exist)  
{{</admonition>}}





