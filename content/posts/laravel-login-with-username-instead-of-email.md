---
title: "Laravel Login With Username Instead of Email"

date: 2021-05-06T13:36:03+09:00

author: CozyFex

categories:

- laravel

tags:

- laravel
- login
- username

keywords:

- laravel
- login
- username

---

## Laravel Login with username instead of email

### 1. Install Laravel

There's a lot of way to install Laravel.\
In this time, I am going to use `composer` that is a dependency manager for `PHP`.\
`Laravel`을 설치하는 방법에는 여러가지가 있다.\
여기서는 `PHP` 의존성 관리 툴인 `composer`를 이용한다.

```shell
composer create-project laravel/laravel login-project
```

### 2. Install breeze that is a starter kit for authentication.

`breeze` is a starter kit for authentication in `Laravel`\
`breeze`는 라라벨 인증을 위한 스타터 킷이다.

```shell
cd login-project
composer require laravel/breeze --dev
npm install && npm run dev
```

### 3. Migrate

You should migrate database for using `Laravel`\
`Laravel`을 이용하기 위해서 마이그레이션을 해야한다.

```shell
php artisan migrate
```

### 4. Testing default login process with email

Now, you need to test exist login process with email.\
But it's not a thing you should do.\
이제 기존 이메일을 이용하는 로그인 프로세스를 테스트해보자.\
그렇지만 이 단계를 꼭 해야하는 것은 아니다.

### 5. Create migration for username

You need to create a `username` field for using it instead of `email` to login.\
`username`을 사용하기 위해서는 `username` 필드를 생성해야 한다.

```shell
php artisan make:migration add_to_username_to_users --table users
vi database/migrations/YYYY_MM_DD_XXXXXX_add_to_username_to_users.php
```

```php
# database/migrations/YYYY_MM_DD_XXXXXX_add_to_username_to_users.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddUsernameToUsers extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('username')->unique();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn('username');
        });
    }
}
```

### 6. Change User Model

```php
# app/Models/User.php

# Orignal
    protected $fillable = [
        'name',
        'email',
        'password',
    ]

# To
    protected $fillable = [
        'username',
        'name',
        'email',
        'password',
    ]
```

### 7. Change Request

```php
# app/Http/Requests/Auth/LoginRequest.php

# Origin
<?php

namespace App\Http\Requests\Auth;

use Illuminate\Auth\Events\Lockout;
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Support\Str;
use Illuminate\Validation\ValidationException;

class LoginRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'email' => 'required|string|email',
            'password' => 'required|string',
        ];
    }

    /**
     * Attempt to authenticate the request's credentials.
     *
     * @return void
     *
     * @throws \Illuminate\Validation\ValidationException
     */
    public function authenticate()
    {
        $this->ensureIsNotRateLimited();

        if (! Auth::attempt($this->only('email', 'password'), $this->filled('remember'))) {
            RateLimiter::hit($this->throttleKey());

            throw ValidationException::withMessages([
                'email' => __('auth.failed'),
            ]);
        }

        RateLimiter::clear($this->throttleKey());
    }

    /**
     * Ensure the login request is not rate limited.
     *
     * @return void
     *
     * @throws \Illuminate\Validation\ValidationException
     */
    public function ensureIsNotRateLimited()
    {
        if (! RateLimiter::tooManyAttempts($this->throttleKey(), 5)) {
            return;
        }

        event(new Lockout($this));

        $seconds = RateLimiter::availableIn($this->throttleKey());

        throw ValidationException::withMessages([
            'email' => trans('auth.throttle', [
                'seconds' => $seconds,
                'minutes' => ceil($seconds / 60),
            ]),
        ]);
    }

    /**
     * Get the rate limiting throttle key for the request.
     *
     * @return string
     */
    public function throttleKey()
    {
        return Str::lower($this->input('email')).'|'.$this->ip();
    }
}

# To
<?php

namespace App\Http\Requests\Auth;

use Illuminate\Auth\Events\Lockout;
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Support\Str;
use Illuminate\Validation\ValidationException;

class LoginRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'username' => 'required|string',
            'password' => 'required|string',
        ];
    }

    /**
     * Attempt to authenticate the request's credentials.
     *
     * @return void
     *
     * @throws \Illuminate\Validation\ValidationException
     */
    public function authenticate()
    {
        $this->ensureIsNotRateLimited();

        if (! Auth::attempt($this->only('username', 'password'), $this->filled('remember'))) {
            RateLimiter::hit($this->throttleKey());

            throw ValidationException::withMessages([
                'username' => __('auth.failed'),
            ]);
        }

        RateLimiter::clear($this->throttleKey());
    }

    /**
     * Ensure the login request is not rate limited.
     *
     * @return void
     *
     * @throws \Illuminate\Validation\ValidationException
     */
    public function ensureIsNotRateLimited()
    {
        if (! RateLimiter::tooManyAttempts($this->throttleKey(), 5)) {
            return;
        }

        event(new Lockout($this));

        $seconds = RateLimiter::availableIn($this->throttleKey());

        throw ValidationException::withMessages([
            'username' => trans('auth.throttle', [
                'seconds' => $seconds,
                'minutes' => ceil($seconds / 60),
            ]),
        ]);
    }

    /**
     * Get the rate limiting throttle key for the request.
     *
     * @return string
     */
    public function throttleKey()
    {
        return Str::lower($this->input('username')).'|'.$this->ip();
    }
}
```

### 8. Change Controller

```php
# app/Http/Controllers/Auth/RegisteredUserController.php

# Origin
        $request->validate([
            'name'     => 'required|string|max:255',
            'email'    => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|confirmed|min:8',
        ]);

        $user = User::create([
            'name'     => $request->name,
            'email'    => $request->email,
            'password' => Hash::make($request->password),
        ]);

# To
        $request->validate([
            'username' => 'required|string|max:255',
            'name'     => 'required|string|max:255',
            'email'    => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|confirmed|min:8',
        ]);

        $user = User::create([
            'username' => $request->username,
            'name'     => $request->name,
            'email'    => $request->email,
            'password' => Hash::make($request->password),
        ]);
```

### 9. Add username field to register view

```php
# resources/views/auth/register.blade.php

    <!-- Username -->
    <div>
        <x-label for="username" :value="__('Username')" />

        <x-input id="username" class="block mt-1 w-full" type="text" name="username" :value="old('username')" required autofocus />
    </div>
```

### 10. Change login form

```php
# resources/views/auth/login.blade.php

# Origin
    <!-- Email Address -->
    <div>
        <x-label for="email" :value="__('Email')" />

        <x-input id="email" class="block mt-1 w-full" type="email" name="email" :value="old('email')" required autofocus />
    </div>

# To
    <!-- Username -->
    <div>
        <x-label for="username" :value="__('Username')" />

        <x-input id="username" class="block mt-1 w-full" type="text" name="username" :value="old('username')" required autofocus />
    </div>
```

### 11. Test with username

Finally, you can register and login with username.\
Test and using it!\
마침내 사용자 등록과 로그인 유저네임으로 할 수 있게되었다!\
테스트해보고 사용하면 된다!