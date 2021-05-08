---
title: "PHP trait in Class"

date: 2021-05-05T15:53:24+09:00

author: CozyFex

categories:

- php

tags:

- php
- trait

keywords:

- php
- trait

---

## PHP trait in Class

The `trait` keyword is for merging classes.

```php
trait ExampleTrait {
    public function Foo() {
        echo 'FOO';
    }
}

class ExampleClass {
    use ExampleTrait;
}

$classInstance = new ExampleClass();
$classInstance->Foo();
```