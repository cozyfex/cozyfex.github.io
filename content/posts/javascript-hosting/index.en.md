---
title: "Javascript hoisting"

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

## What is the hoisting?

It's a characteristic that is assigned in the top level when it's assigned with var in the interpreting progress.

```javascript
console.log(foo); // undefined

var foo;
foo = 'It is foo.';

console.log(foo); // It is foo.
```

This characteristic occurs wrong situation like below code.

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
So we noticed hoisting is working in each scope.

How about assign a function?

```javascript
console.log(typeof foo); // function
foo(); // hoisting function test
function foo() {
    console.log('hoisting function test');
}

console.log(typeof foo); // function
foo(); // hoisting function test
```

Then how about when you assign the function to a value?

```javascript
console.log(typeof foo); // undefined
var foo = function () {
    console.log('hoisting function test');
}
console.log(typeof foo); // function
```

Is the result you expected?
