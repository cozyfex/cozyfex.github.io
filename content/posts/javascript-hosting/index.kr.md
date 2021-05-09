---
title: "Javascript 호이스팅"

date: 2012-04-25T12:11:00+0900

author: CozyFex

categories:

- javascript

tags:

- javascript
- hoisting

keywords:

- javascript
- hoisting

---

## 호이스팅이란?

인터프리팅 과정에서 var로 선언된 것들은 모두 최상위에서 이미 선언한 것으로 간주하는 특성

```javascript
console.log(foo); // undefined

var foo;
foo = 'It is foo.';

console.log(foo); // It is foo.
```

이 특성때문에 아래와 같은 오작동 현상이 발생할 수 있다.

```javascript
console.log(foo); // undefined

var foo;
foo = 3;

console.log(foo); // 3

function bar() {
    // Look at here!
    console.log(foo); // undefined

    var foo;
    foo = 'It is foo.';
    console.log(foo); // It is foo.
}

bar();
```

"Loot at here!"에서 undefined가 출력되는 이유는 function 안에서 foo가 다시 선언되어 함수 내 최상위에서 다시 호이스팅 되어서 지역변수 스코프의 foo를 참조 때문이다.\
이 예제로 우리는 호이스팅은 각각의 스코프마다 작동한는것을 알 수 있다.

함수 선언은 어떨까?

```javascript
console.log(typeof foo); // function
foo(); // hoisting function test
function foo() {
    console.log('hoisting function test');
}

console.log(typeof foo); // function
foo(); // hoisting function test
```

그렇다면 변수에 함수를 할당해보면 어떨까?

```javascript
console.log(typeof foo); // undefined
var foo = function () {
    console.log('hoisting function test');
}
console.log(typeof foo); // function
```

예상한 데로 인가요?
