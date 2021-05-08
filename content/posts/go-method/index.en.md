---
title: "Go Method"

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

# Method in Go

## What is mean of `method`?

The `method` means a function of a class in OOP.

## Value vs Pointer receiver

There are two ways to receive the structure.

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
	println(rect.areaWithValue()) // 220
	println(rect.width) // 10
	println(rect.areaWithPointer()) // 220
	println(rect.width) // 11
}
```

