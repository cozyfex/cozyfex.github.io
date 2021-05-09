---
title: "Go 메소드"

date: 2021-04-18T22:22:13+09:00

author: CozyFex

categories:

- go

tags:

- go
- method

keywords:

- go
- method

---

## `메소드`란?

`메소드`란 객체지향 프로그래밍(OOP)에서 클래스의 함수를 의미한다.

## Value(값) vs Pointer(주소) receiver

구조체를 받는 방법은 두가지가 있다.

```go
package main

type Rect struct {
	width, height int
}

// Value receiver
func (r Rect) areaWithValue() int {
	r.width++
	return r.width * r.height
}

// Pointer receiver
func (r *Rect) areaWithPointer() int {
	r.width++
	return r.width * r.height
}

func main() {
	rect := Rect{10, 20}
	println(rect.areaWithValue())   // 220
	println(rect.width)             // 10
	println(rect.areaWithPointer()) // 220
	println(rect.width)             // 11
}
```

