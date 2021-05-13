# PHP Composer Autoload


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

