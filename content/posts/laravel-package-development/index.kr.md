---
title: "Laravel 패키지 개발"

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

## 필요 사전 직식

### Composer

{{<admonition note Composer true>}}  
`Composer` 는 PHP 에서의 의존선 관리 툴이다.  
{{</admonition>}}

### Packagist

{{<admonition note Packagist true>}}  
`Packagist` 는 `Composer` 의 기본 저장소이다.  
{{</admonition>}}

### Laravel

{{<admonition note Laravel true>}}  
`Laravel` 는 PHP 기반의 웹 어플리케이션 프레임워크이다.  
{{</admonition>}}

### GitHub

{{<admonition note GitHub true>}}  
`GitHub` 는 버전관리와 협업을 위한 코드 호스팅 플렛폼이다.  
{{</admonition>}}

## Laravel 설치

{{<admonition note Note true>}}  
이 포스트에서는 어떻게 `PHP` 와 `Composer` 를 세팅하는지 설명하지 않는다.  
{{</admonition>}}

```shell
composer create-project laravel/laravel my-project
```

## 패키지 개발을 위한 설정

### 디렉토리 구조

{{<admonition note "Directory Structure" true>}}  
`<Laravel Project>`/`<Packages Direcotry>`/`<Vendor Name>`/`<Package Name>`/src  
{{</admonition>}}

#### Packages 디렉토리 생성

```shell
cd my-project
mkdir packages
```

#### Vendor 디렉토리 생성

```shell
cd packages
mkdir cozyfex
```

#### 패키지 디렉토리 생성

```shell
cd cozyfex
mkdir my-package
```

### 소스 디렉토리 생성

```shell
cd my-package
mkdir src
```

### `git` 초기화

#### GitHub 초기화

- `GitHub` 사이트 가입
- `laravel-my-package` 저장소 생성

#### `git` 로컬 초기화

```shell
# my-project/packages/cozyfex/my-package

# Init
git init

# Setting ignore
vi .gitignore

# Add remote repository, below is example for this post.
git remote add origin git@github.com:cozyfex/laravel-my-package.git
```

#### 로컬과 GitHub 연동 확인

##### 저장소에 푸쉬

```shell
git add -A
git commit -m "First commit"
git push -u origin master
```

##### GitHub 저상소 확인

- GitHub 로그인 후 저장소 확인!

### `composer` 초기화

#### `composer` 사용

```shell
# my-project/packages/cozyfex/my-package
composer init
```

#### 템플릿 사용

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

#### 핵심 속성

{{<admonition note Note true>}}  
`composer.json` 에 대한 더 상세한 속성들을 알고싶으면, 공식 사이트 [Composer Document](https://getcomposer.org/)를 확인한다.  
{{</admonition>}}

##### `name`

{{<admonition note name true>}}  
`<vendor_name>`/`<package_name>`
{{</admonition>}}

##### `support`

{{<admonition note support true>}}  
지원 URL  
{{</admonition>}}

##### `authors`

{{<admonition note authors true>}}  
저자 정보  
{{</admonition>}}

##### `require`

{{<admonition note require true>}}  
`composer` 의 주요 속성이다.  
이 정보로 프로젝트나 라이브러리의 의존성을 해결한다.  
이 패키지는 `laravel/framework` 를 의존한다.  
{{</admonition>}}

##### `autoload`

{{<admonition note autoload true>}}  
이 설정은 패키지 소스들을 `autoload` 하기 위한 설정이다.  
{{</admonition>}}

##### `extra`

{{<admonition note extra true>}}  
이 설정은 패키지 프로바이더를 `laravel` 에 제공한다.  
우리는 이 포스트에서 추후에 `PackageServiceProvider` 파일을 생성할 것이다.  
{{</admonition>}}

## 패키지 소스 생성

### Provider 생성

```shell
# my-project
php artisan make:provider PackageServiceProvider
mv app/Providers/PackageServiceProvider.php packages/cozyfex/my-package/src/
```

### Views 생성

#### 디렉토리 생성

```shell
# my-project/packages/cozyfex/my-package/src
mkdir views

cd views
# my-project/packages/cozyfex/my-package/src/views
mkdir MyPackage
```

#### View 파일 생성

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

#### Provider 에 View 설정

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

### Controllers 생성

#### Directory 생성

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

### Routes 생성

#### `routes.php` 생성

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
여기서 `middleware` 에 관해 알려줄게 있다.  
위의 소스를 보면 2개의 `middleware` 가 있다.  
`web` 과 `auth` 이다.  
`web` 은 웹 세션에 접근하기 위한 것이다.(`web` 미들웨어세서 세션을 시작한다.)  
`web` 을 사용하지 않으면 패키지 내에서 세션에 접근 할 수가 없다.  
이 말은, `web` 없이 `auth` 만을 사용하면 `auth` 미들웨어는 제대로 동작하지 않는다.

더 자세한 사항은 [공식 문서](https://laravel.com/docs/8.x/middleware) 를 확인한다.  
{{</admonition>}}

#### Provider 에 Routes 설정

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

## Laravel 과 Package 연결

### Laravel `composer.json` 에 Autoload 등록

<sub>my-project/composer.json</sub>

{{<admonition note "확인!" true>}}  
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

### Vendor Autoload 생성

{{<admonition note "중요!" true>}}  
이 부분은 매우 중요하다!!  
잊지 말자!!  
{{</admonition>}}

```shell
# my-project
composer dump-autoload
```

### Laravel Providers 에 Provider 추가

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

## 중간 점검

{{<admonition note Links true>}}  
`php artisan serve` 실행

이제 아래의 링크를 확인할 수 있다.

`http://localhost:8000/none`  
잘 동작한다.

`http://localhost:8000/exist`  
이 링크는 동작하지 않는다.  
그러나 걱정하지 말자, 이미 예측된 일이다.  
로그인 관련된 어떤 프로세스도 설치하지 않았기 때문이다.  
만약 전부 동작하길 원한다면, 아래의 명령어들을 프로젝트 디렉토리에서 실행하자.  
`composer require laravel/breeze`  
`php artisan breeze:install`  
`npm install && npm run dev`  
{{</admonition>}}

## 추가 기능

### Migration

{{<admonition note Migration true>}}  
이 부분에 들어가기 전에, `Laravel` 의 데이터베이스 연결이 되어야 한다.  
{{</admonition>}}

#### `migrations` 디렉토리 생성

```shell
# my-project/packages/cozyfex/my-package/src
mkdir migrations
```

#### Migration 파일 생성

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

#### Provider 에 Migrations 설정

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

#### `Models` 디렉토리 생성

```shell
# my-project/packages/cozyfex/my-package/src
mkdir Models
```

#### Model 생성

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

#### `Commands` 디렉토리 생성

```shell
# my-project/packages/cozyfex/my-package/src
mkdir Commands
```

#### Command 생성

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

#### Provider 에 Command 설정

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

#### Command 확인

```shell
# my-project
php artisan

# Only cozyfex
php artisan | grep cozyfex
```

### Publish PackageServiceProvider

{{<admonition note "Publish in Laravel" true>}}  
이 패키지가 여러분들의 프로젝트에 완벽히 맞는것인지 보장 할 수 없다.  
몇몇은 이 패키지를 사용하길 원하지만, 또한 패키지에서 일부분을 변경하고 싶어한다.  
이런 이유들로 `Laravel` 에서는 해당 기능을 `Provider` 에서 제공한다.  
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

#### Laravel Publish 경로 관련 함수

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

#### Publish 명령

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

#### 배포된 디렉토리 확인

```shell
cd my-project/resources/views
```

## GitHub 에 퓌쉬

### Commit

```shell
git status
git add -A
git commit -m "My package"
git push origin master
```

### Tagging

{{<admonition note "For Packagist Versioning" true>}}  
이 태그는 Packagist 버전에 사용된다.  
{{</admonition>}}

```shell
git tag v1.0.0
```

### Push Tag

```shell
git push origin v1.0.0
```

## `packagist.org` 에 패키지 등록

{{<admonition note "추천" true>}}  
GitHub 계정으로 가입하기를 추천한다.  
`packagist.org` 가 GitHub 에 `web hook` 을 등록해 준다.  
당연히, 동의가 필요하다.  
{{</admonition>}}

{{<mermaid>}}  
graph TD;  
L(Login 'packagist.org') --> S(Go to 'Submit' menu on right top side)  
S --> C(Copy and Paste GitHub repository URL to input box that is above 'Check' button)  
C --> CC(Click 'Check' button)  
CC --> M(Click `Submit` button)  
{{</mermaid>}}

## 새 Laravel 프로젝트에서 패키지 확인!

### 새 Laravel 프로젝트 생성

```shell
composer create-project laravel/laravel new-project
```

### Composer Require MyPackage

```shell
# new-project
cd new-project
composer require cozyfex/my-package
```

### 실행 및 링크 확인

```shell
php artisan serve
```

{{<admonition note Links true>}}  
[http://localhost:8000/none](http://localhost:8000/none)  
[http://localhost:8000/exist](http://localhost:8000/exist)  
{{</admonition>}}





