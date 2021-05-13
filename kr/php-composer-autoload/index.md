# PHP Composer Autoload


## Composer

This is a package manager in `PHP`.  
The official site is a [here](https://getcomposer.org)

## Composer 초기화

```shell
composer init
```

## `composer.json` 설정

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

## Composer 설정 적용

```shell
composer dump-autoload
```

