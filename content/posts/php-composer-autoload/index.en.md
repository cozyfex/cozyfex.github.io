---
title: "PHP Composer Autoload"

date: 2021-05-13T17:57:20+09:00

author: CozyFex

categories:

- php
- composer
- autoload

tags:

- php
- composer
- autoload
- config custom path

keywords:

- php
- composer
- autoload
- config custom path

---

## Composer

This is a package manager in `PHP`.  
The official site is a [here](https://getcomposer.org)

## Initialize Composer

```shell
composer init
```

## Configuration `composer.json`

```json
{
  "name": "<vendor>/<name>",
  "type": "<type>",
  "authors": [
    {
      "name": "<name>",
      "email": "<email>"
    }
  ],
  "autoload": {
    "psr-4": {
      "App\\": "<path/to/class>"
    }
  },
  "description": "<description>"
}
```

## Apply Configuration to Composer

```shell
composer dump-autoload
```
