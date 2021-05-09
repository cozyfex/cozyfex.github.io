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

## 1. Install Laravel

There's a lot of way to install Laravel.\
In this time, I am going to use `composer` that is a dependency manager for `PHP`.

```shell
composer create-project laravel/laravel login-project
```

## 2. Install breeze that is a starter kit for authentication.

`breeze` is a starter kit for authentication in `Laravel`

```shell
cd login-project
composer require laravel/breeze --dev
npm install && npm run dev
```

## 3. Migrate

You should migrate database for using `Laravel`

```shell
php artisan migrate
```

## 4. Testing default login process with email

Now, you need to test exist login process with email.\
But, it's not a thing you should do.

## 5. Create migration for username

You need to create a `username` field for using it instead of `email` to login.

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

## 6. Change User Model

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

## 7. Change Request

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

## 8. Change Controllers

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

## 9. Add username field to register view

<sub>resources/views/auth/register.blade.php</sub>

```php
# resources/views/auth/register.blade.php

    <!-- Username -->
    <div>
        <x-label for="username" :value="__('Username')" />

        <x-input id="username" class="block mt-1 w-full" type="text" name="username" :value="old('username')" required autofocus />
    </div>
```

## 10. Change login form

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

## 11. Test with username

Finally, you can register and login with username.\
Test and using it!