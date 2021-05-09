---
title: "Laravel Often Used Artisan Commands"

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

## Create Controller

```shell
# Create Controller
php artisan make:controller Auth/LoginController

# Created a File
vi app/Http/Controllers/Auth/LoginController.php
````

## Create Model with Create Table Migration

```shell
# Create Model with Migration File by Model
php artisan make:model Board -m

# Created Files
vi app/Models/Board.php
vi database/migrations/YYYY_MM_DD_000000_create_boards_table.php
```

## Create Modify Table Migration

```shell
# Create Migration File for Modify [users] table
php artisan make:migration add_username_to_users --table users

# Created a File
vi database/migrations/YYYY_MM_DD_000000_add_username_to_users.php
```

## Create Model with Factory, Migration, Seeder, and Controller

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

## Apply to the Database from Migration File

```shell
php artisan migrate
```

## Run Development Web Server

```shell
php artisan serve
```

## Run Test

```shell
php artisan test
```