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

## Laravel Directory Structure

> This document is for Laravel 8.x version and comes from official site [Laravel](https://laravel.com/docs/8.x/structure).  
> 이 문서는 Laravel 8.x 버전을 위한 것이고 공식 사이트의 문서를 요약한 것입니다.

### `app`

> The `app` directory contains the core code of your application.  
> `app` 디렉토리는 코어 코드를 포함하고 있다.
> #### `app/Broadcasting`
>> The `Boardcasting` directory contains all of the broadcast channel classes for your application.
> These classes are generated using the `make:channel` command.
> This directory does not exist by default, but will be created for you when you create your first channel.
> `Broadcasting` 디렉토리는 어플리케이션의 모든 브로드케스트 채널을 포함한다.
> 이 클래스들은 `make:chnnel` 명령어로 생성된다.
> 기본적으로 이 디렉토리는 존재하지 않지만, 채널을 생성 할 때 만들어진다.
> #### `app/Console`
>> The `Console` directory contains all of the custom Artisan commands for your application.
> These commands may be generated using the `make:command` command.  
> `Console` 디렉토리는 커스텀 아티즌 명령들이 있다.
> `make:command` 명령오로 생성할 수 있다.
> #### `app/Events`
>> This directory does not exist by default, but will be created for you by the `event:generate` and `make:event` Artisan commands.
> The `Events` directory houses event classes.  
> 이 디렉토리는 기본적으로 존재하지 않지만 `event:generate`와 `make:event` 아티즌 명령어로 만들 수 있다.
> `Events` 디렉토리는 이벤트 클래스들이 위치한다.
> #### `app/Exceptions`
>> The `Exceptions` directory contains your application's exception handler and is also a good place to place any exceptions thrown by your application.  
> `Exceptions` 디렉토리는 어플리케이션의 예외 처리와 어떤 다른 예외들을 처리 할 수 있는 좋은 위치이다.
> #### `app/Http`
>> The `Http` directory contains your controllers, middleware, and form requests.  
> `Http` 디렉토리는 어플리케이션의 컨트롤러, 미들웨어와 폼 요청을 포함한다.
> #### `app/Jobs`
>> This directory does not exist by default, but will be created for you if you execute the `make:job` Artisan command.
> The `Jobs` directory houses the queueable jobs for your applications.  
> 이 디렉토리는 기본으로 존재하지 않지만, `make:job` 아티즌 명령어를 실행하면 생성된다.
> `Jobs` 디렉토리는 대기가능한 작업들이 위치한다.
> #### `app/Listeners`
>> This directory does not exist by default, but will be created for you if you execute the `event:generate` or `make:listener` Artisan commands.
> The `Listeners` directory contains the classes that handle your events.  
> 이 디렉토리는 기본적으로 존재하지 않지만, `event:generate`나 `make:listener` 아티즌 명령을 실행하면 생성된다.
> `Listener` 디렉토리는 이벤트 핸들러 클래스들이 위치한다.
> #### `app/Mail`
>> This directory does not exist by default, but will be created for you if you execute the `make:mail` Artisan command.  
> 이 디렉토리는 기본적으로 존재하지 않지만, `make:mail` 아티즌 명령어를 실행하면 생성된다.
> #### `app/Models`
>> The `Models` directory contains all of your Eloquent model classes.
> The Eloquent ORM included with Laravel provides a beautiful, simple ActiveRecord implementation for working with your database.  
> `Models` 디렉토리는 Eloquent 모델 클래스가 있다.
> Eloquent ORM은 Laravel이 제공하는 데이터베이스와 일하는 아름답고 간단한 ORM이다.
> #### `app/Notifications`
>> This directory does not exist by default, but will be created for you if you execute the `make:notification` Artisan command.
> It can be email, Slack, SMS, or stored in a database.  
> 이 디렉토리는 기본적으로 존재하지 않지만, `make:notification` 아티즌 명령을 실행하면 생성된다.
> 이메일, Slack, SMS, 또는 데이터베이스 저장이 있다.
> #### `app/Polices`
>> This directory does not exist by default, but will be created for you if you execute the `make:policy` Artisan command.
> The `Policies` directory contains the authorization policy classes for your application.  
> 이 디렉토리는 기본적으로 존재하지 않지만, `make:policy` 아티즌 명령으로 생성된다.
> `Policy` 디렉토리는 인증 정책 클래스들이 위치한다.
> #### `app/Providers`
>> The `Providers` directory contains all of the service providers for your application.  
> `Providers` 디렉토리는 모든 서비스 프로바이더를 포함한다.
> #### `app/Rules`
>> This directory does not exist by default, but will be created for you if you execute the `make:rule` Artisan command.
> The `Rules` directory contains the custom validation rule objects for your application.  
> 이 디렉토리는 기본적으로 존재하지 않지만, `make:rule` 아티즌 명령으로 생성된다.
> `Rules` 디렉토리는 커스텀 검증 규직 객체가 있는곳이다.

### `bootstrap`

> The `bootstrap` directory contains `app.php` file which bootstraps framework.  
> The directory also houses a `cache` directory which contains framework generated files.  
> `bootstrap` 디렉토리는 부트스트렙 프레임워크의 `app.php` 파일을 포함하고 있다.  
> 이 디렉토리에는 프레임워크가 생성한 `cache` 디렉토리를 가지고있다.

### `config`

> The `config` directory, as the name implies, contains all of your application's configuration files.  
> `config` 디렉토리는, 이름 그대로, 어플리케이션의 모든 설정파일들이 있는 곳이다.

### `database`

> The `database` directory contains your database migrations, model factories, and seeds.  
> `database` 디렉토리는 모든 데이터베이스 마이그레션과 모델들을 가지고 있다.

### `public`

> The `public` directory contains the `index.php` file, which is the entry point for all requests entering your application and configures autoloading. This direcotry also houses your assets such as images, JavaScript, and CSS.  
> `public` 디렉토리는 모든 요청과 설정의 오토로딩의 시작점인 'index.php' 파일이 위차한 곳이다. 이 디렉토리는 이미지, JavaScript, CSS 등을 포함한다.

### `resources`

> The `resources` directory contains your views as well as your raw, un-compiled assets such as CSS or JavaScript. This directory also houses all of your language files.  
> `resource` 디렉토리는 뷰, 컴파일 되지 않은 JavaScript, CSS 파일같은 것들이 위치한다. 그리고 이 디렉토리는 언어 파일들도 위치한다.

### `routes`

> The `routes` directory contains all of the route definitions for your applications.
> By default, several route files are included with Laravel: `web.php`, `api.php`, `console.php`, and `channels.php`.  
> `routes` 디렉토리는 모든 경로 정의들을 가지고있다. 기본적으로 몇가지 경로 설정 파일들을 포함하고 있다.
> `web.php`, `api.php`, `console.php`, `channels.php` 파일들을 가지고 있다.
>
> #### `routes/web.php`
>> The `web.php` file contains routes that the `RouteServiceProvider` places in the `web` middleware group, which provides session state, CSRF protection, and cookie encryption.
> If your application does not offer a stateless, RESTful API then it is likely that all of your routes will most likely be defined in the `web.php` file.  
> `web.php` 파일은 세션 상태, CSRF 보호, 쿠키 암호화를 제공하는 `RouteServiceProvider` 에 위치한 `web` 미들웨어 그룹의 경로들을 가지고있다.
> RESTful이나 stateless 에 관한 요청이 없으면 대부분의 정의는 `web.php` 파일에서 한다.
>
> #### `routes/api.php`
>
>> The `api.php` file contains routes that the `RouteServiceProvider` places in the `api` middleware group.
> These routes are intended to be stateless, so requests entering the application through these routes are intended to be authenticated via token and will not have access to session state.  
> `api.php` 파일은 `api` 미들웨어 그룹에 위치한 `RouteServiceProvider`의 경로를 포함한다.
> 이 경로들은 상태가 없는 요청을 위한것이고, 토큰을 통해 인증한다.
>
> #### `routes/console.php`
>> The `console.php` file is all of your closure based console commands.  
> `console.php` 파일은 모든 폐쇠된 콘솔 기반을 포함한다.
>
> #### `routes/channels.php`
>> The `channels.php` file is where you may register all of the event broadcasting channels that your application supports.  
> `channels.php` 파일은 어플리케이션 지원하는 모든 이벤트 브로드케스팅을 등록하는 곳이다.

### `storage`

> The `storage` directory contains your logs, compiled Blade templates, file based sessions, file caches, and other files generated by the framework.  
> `storage` 디렉토리는 로그, 컴파일된 템플릿, 파일 기반 세션, 캐쉬 파일, 프레임워크가 생성한파일들이 위치한다.
> #### `storage/app`
>> Application generated files.  
> 어플리케이션이 생성한 파일들 위치.
> #### `storage/framework`
>> Framework generated files and caches.  
> 프레임워크가 생성한 파일이나 캐쉬들이 위치.
> #### `storage/logs`
>> Application's log files.
> 어플리케이션 로그 파일들 위치.

### `tests`

> The `tests` directory contains your automated tests.  
> `tests` 디렉토리는 자동화된 테스트들이 위치힌다.

### `vendor`

> The `vender` directory contains your `Composer` dependencies.  
> `vender` 디렉토리는 컴포저 의존성을 포함한다.


