---
title: "Go Module, Package, Import"

date: 2023-07-09T20:24:36+09:00

author: CozyFex

categories:

  - go

tags:

  - go
  - go package
  - go import

keywords:

  - go
  - go package
  - go import

---

## Module

- Module is a collection of packages.
- It is better to use the module name in `Domain/Module` format.
- The module naming convention is **lowercase** and **single-word**.

It should be declared in the top of `go.mod` file.

```
module cozyfex.com/sample1

go 1.20
```

{{< admonition type=tip title="GOPATH" open=true >}}
It is recommended that the package name of `Root Path` of the module be the same as `Module`.
{{< /admonition >}}

----

## Package

- The package name is the directory's name that the file exist.
- It should be same package name for files that same path.
- The package naming convention is **lowercase** and **single-word**.

----

## Import

- It is possible to `import` per package.
- Even a `package` under the same project is imported as a `package` under `module` declared in `go.mod`.
- For using external package it should be declared in `go.mod` file.
- The `require` of `go.mod` file is finding the module in `GOPATH`.
- Modules that cannot be installed with `go get` can be imported using `require` and `replace`.

### Importing local module

Suppose there are two modules below.

- `cozyfex.com/sample1`
- `cozyfex.com/sample2`

#### cozyfex.com/sample1

##### File Structure

```
│ go.mod
│ what.go
│ sub
  │  who.go
  └─ directory
     │  where.go
```

##### File Contents

###### `go.mod`

```
module cozyfex.com/sample1

go 1.20
```

###### `what.go`

```go
package sample1

import (
	"fmt"

	"cozyfex.com/sample1/sub"
	"cozyfex.com/sample1/sub/directory"
)

func Name() {
	sub.Who()
	directory.Where()
	fmt.Println("This is sample1 module")
}

func What() {
	fmt.Println("What")
}
```

> As you can see here, when you `import` a package in the same project, it imports based on the module name declared in
> the `go.mod` file.

###### `sub/who.go`

```go
package sub

import (
	"fmt"
)

func Who() {
	fmt.Println("Who")
}
```

###### `sub/directory/where.go`

```go
package directory

import (
	"fmt"
)

func Where() {
	fmt.Println("Where")
}
```

#### cozyfex.com/sample2

##### File Structure

```
│  go.mod
│  main.go
```

##### File Contents

###### `go.mod`

```
module cozyfex.com/sample2

go 1.20

require cozyfex.com/sample1 v0.0.0

replace cozyfex.com/sample1 v0.0.0 => ../sample1
```

> To `import` a local module, `require` and `replace` are used to match the module name with the path.

###### `main.go`

```go
package main

import (
	"cozyfex.com/sample1"
	"cozyfex.com/sample1/sub"
	"cozyfex.com/sample1/sub/directory"
)

func main() {
	sample1.Name()
	sample1.What()
	sub.Who()
	directory.Where()
}
```

#### Explanation

##### Scenario

This is an example of trying to use the packages of the sample1 module in sample2.  
Both sample1 and sample2 are located locally, and sample1 cannot be installed with `go get`.

##### 해결책

In the `go.mod` file of sample2, `replace` the path of the sample1 module to a place other than `GOPATH`.  
Among the long articles above, the most important points are the following.

```
require cozyfex.com/sample1 v0.0.0
replace cozyfex.com/sample1 v0.0.0 => ../sample1
```

As with other modules, `require` it, then use `replace` to change the path.
In this case, both absolute and relative paths can be used for the path.
