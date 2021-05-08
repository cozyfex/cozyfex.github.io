# PHP 클레스에서의 trait


## PHP 클래스에서의 trait

`trait` 키워드는 클래스들을 합치기 위한 것이다.

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
