---
title: "Laravel 이메일 대신 아이디로 로그인 하기"

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

## Laravel 이메일 대신 아이디로 로그인 하기

### 1. Laravel 설치

`Laravel`을 설치하는 방법에는 여러가지가 있다.\
여기서는 `PHP` 의존성 관리 툴인 `composer`를 이용한다.

```shell
composer create-project laravel/laravel login-project
```

### 2. 인증을 위한 breeze 스타터킷 설치

`breeze`는 라라벨 인증을 위한 스타터 킷이다.

```shell
cd login-project
composer require laravel/breeze --dev
npm install && npm run dev
```

### 3. 마이그레이트

`Laravel`을 이용하기 위해서 마이그레이션을 해야한다.

```shell
php artisan migrate
```

### 4. 기본 이메일 로그인 프로세스 테스트

이제 기존 이메일을 이용하는 로그인 프로세스를 테스트해보자.\
그렇지만 이 단계를 꼭 해야하는 것은 아니다.

### 5. username을 위한 마이그레이션 생성하기

`username`을 사용하기 위해서는 `username` 필드를 생성해야 한다.

```shell
php artisan make:migration add_to_username_to_users --table users
vi database/migrations/YYYY_MM_DD_XXXXXX_add_to_username_to_users.php
```

<sub>database/migrations/YYYY_MM_DD_XXXXXX_add_to_username_to_users.php</sub>

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

### 6. User 모델 변경

<sub>app/Models/User.php</sub>

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

### 7. Request 변경

<sub>app/Http/Requests/Auth/LoginRequest.php</sub>

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

### 8. Controllers 변경

<sub>app/Http/Controllers/Auth/RegisteredUserController.php</sub>

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

<sub>app/Http/Controllers/Auth/ConfirmablePasswordController.php</sub>

```php
# app/Http/Controllers/Auth/ConfirmablePasswordController.php

# Origin
        if (! Auth::guard('web')->validate([
            'email' => $request->user()->email,
            'password' => $request->password,
        ])) {
            throw ValidationException::withMessages([
                'password' => __('auth.password'),
            ]);
        }

# To
        if (! Auth::guard('web')->validate([
            'username' => $request->user()->username,
            'password' => $request->password,
        ])) {
            throw ValidationException::withMessages([
                'password' => __('auth.password'),
            ]);
```

<sub>app/Http/Controllers/Auth/NewPasswordController.php</sub>

```php
# app/Http/Controllers/Auth/NewPasswordController.php

# Origin
     public function store(Request $request)
    {
        $request->validate([
            'token' => 'required',
            'email' => 'required|email',
            'password' => 'required|string|confirmed|min:8',
        ]);

        // Here we will attempt to reset the user's password. If it is successful we
        // will update the password on an actual user model and persist it to the
        // database. Otherwise we will parse the error and return the response.
        $status = Password::reset(
            $request->only('email', 'password', 'password_confirmation', 'token'),
            function ($user) use ($request) {
                $user->forceFill([
                    'password' => Hash::make($request->password),
                    'remember_token' => Str::random(60),
                ])->save();

                event(new PasswordReset($user));
            }
        );

        // If the password was successfully reset, we will redirect the user back to
        // the application's home authenticated view. If there is an error we can
        // redirect them back to where they came from with their error message.
        return $status == Password::PASSWORD_RESET
                    ? redirect()->route('login')->with('status', __($status))
                    : back()->withInput($request->only('email'))
                            ->withErrors(['email' => __($status)]);
    }

# To
    public function store(Request $request)
    {
        $request->validate([
            'token' => 'required',
            'username' => 'required',
            'password' => 'required|string|confirmed|min:8',
        ]);

        // Here we will attempt to reset the user's password. If it is successful we
        // will update the password on an actual user model and persist it to the
        // database. Otherwise we will parse the error and return the response.
        $status = Password::reset(
            $request->only('username', 'password', 'password_confirmation', 'token'),
            function ($user) use ($request) {
                $user->forceFill([
                    'password' => Hash::make($request->password),
                    'remember_token' => Str::random(60),
                ])->save();

                event(new PasswordReset($user));
            }
        );

        // If the password was successfully reset, we will redirect the user back to
        // the application's home authenticated view. If there is an error we can
        // redirect them back to where they came from with their error message.
        return $status == Password::PASSWORD_RESET
                    ? redirect()->route('login')->with('status', __($status))
                    : back()->withInput($request->only('username'))
                            ->withErrors(['username' => __($status)]);
    }
```

<sub>app/Http/Controllers/Auth/PasswordResetLinkController.php</sub>

```php
# app/Http/Controllers/Auth/PasswordResetLinkController.php

# Origin
    public function store(Request $request)
    {
        $request->validate([
            'email' => 'required|email',
        ]);

        // We will send the password reset link to this user. Once we have attempted
        // to send the link, we will examine the response then see the message we
        // need to show to the user. Finally, we'll send out a proper response.
        $status = Password::sendResetLink(
            $request->only('email')
        );

        return $status == Password::RESET_LINK_SENT
                    ? back()->with('status', __($status))
                    : back()->withInput($request->only('email'))
                            ->withErrors(['email' => __($status)]);
    }

# To
    public function store(Request $request)
    {
        $request->validate([
            'username' => 'required',
        ]);

        // We will send the password reset link to this user. Once we have attempted
        // to send the link, we will examine the response then see the message we
        // need to show to the user. Finally, we'll send out a proper response.
        $status = Password::sendResetLink(
            $request->only('username')
        );

        return $status == Password::RESET_LINK_SENT
                    ? back()->with('status', __($status))
                    : back()->withInput($request->only('username'))
                            ->withErrors(['username' => __($status)]);
    }
```

### 9. 등록 뷰에 username 필드 추가

<sub>resources/views/auth/register.blade.php</sub>

```php
# resources/views/auth/register.blade.php

    <!-- Username -->
    <div>
        <x-label for="username" :value="__('Username')" />

        <x-input id="username" class="block mt-1 w-full" type="text" name="username" :value="old('username')" required autofocus />
    </div>
```

### 10. 로그인 폼 변경

<sub>resources/views/auth/login.blade.php</sub>

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

### 11. username으로 로그인 테스트

마침내 사용자 등록과 로그인 유저네임으로 할 수 있게되었다!\
테스트해보고 사용하면 된다!