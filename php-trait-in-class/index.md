# PHP trait in Class


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
