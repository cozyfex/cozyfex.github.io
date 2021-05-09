---
title: "Laravel 자주 사용하는 아티즌 명령"

date: 2021-05-05T16:27:14+09:00

author: CozyFex

categories:

- laravel

tags:

- laravel
- artisan
- command

keywords:

- laravel
- artisan
- command

---

## Controller 생성

```shell
# Create Controller
php artisan make:controller Auth/LoginController

# Created a File
vi app/Http/Controllers/Auth/LoginController.php
````

## 모델과 테이블 생성 마이그레이션 한번에 생성

```shell
# Create Model with Migration File by Model
php artisan make:model Board -m

# Created Files
vi app/Models/Board.php
vi database/migrations/YYYY_MM_DD_000000_create_boards_table.php
```

## 테이블 수정 마이그레이션 생성

```shell
# Create Migration File for Modify [users] table
php artisan make:migration add_username_to_users --table users

# Created a File
vi database/migrations/YYYY_MM_DD_000000_add_username_to_users.php
```

## 모델과 함께 팩토리, 마이그레이션, 시더, 컨트롤러 한번에 생성

```shell
# Create Model, Factory, Migration, Seeder, and Controller for Profile
php artisan make:model Cozy/Profile -a

# Created Files
vi app/Models/Cozy/Profile.php
vi database/factories/Cozy/ProfileFactory.php
vi database/migrations/YYYY_MM_DD_000000_create_profiles_table.php
vi database/seeders/ProfileSeeder.php
vi app/Http/Controllers/ProfileController.php
```

## 마이그레이션들을 데이터베이스에 적용

```shell
php artisan migrate
```

## 개발 웹서버 실행

```shell
php artisan serve
```

## 테스트 실행

```shell
php artisan test
```