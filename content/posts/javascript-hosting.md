---
title: "Javascript Hosting"

date: 2012-04-25T12:11:00+0900

author: CozyFex

categories:

- javascript

tags:

- javascript
- hosting

keywords:

- javascript
- hosting

---

## What is the hosting? / 호이스팅이란?

It's a characteristic that is assigned in the top level when it's assigned with var in the interpreting progress.\
인터프리팅 과정에서 var로 선언된 것들은 모두 최상위에서 이미 선언한 것으로 간주하는 특성

```javascript
console.log(foo); // undefined

var foo;
foo = 'It is foo.';

console.log(foo); // It is foo.
```

This characteristic occur wrong situation like below code.\
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

The reason why it's printed as undefined, it referenced foo of function region scope in the function.\
So we noticed hosting is working in each scope.\
"Loot at here!"에서 undefined가 출력되는 이유는 function 안에서 foo가 다시 선언되어 함수 내 최상위에서 다시 호이스팅 되어서 지역변수 스코프의 foo를 참조하기 때문이다.\
이 예제로 우리는 호이스팅은 각각의 스코프마다 작동한는것을 알 수 있다.\

How about assign a function?\
함수 선언은 어떨까?

```javascript
console.log(typeof foo); // function
foo(); // hosting function test
function foo() {
    console.log('hosting function test');
}

console.log(typeof foo); // function
foo(); // hosting function test
```

Then how about when you assign the function to a value?\
그렇다면 변수에 함수를 할당해보면 어떨까?

```javascript
console.log(typeof foo); // undefined
var foo = function () {
    console.log('hosting function test');
}
console.log(typeof foo); // function
```

Is the result you expected?\
예상한 데로 인가요?
