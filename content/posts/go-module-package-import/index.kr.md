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

- 모듈은 패키지들의 모음이다.
- 모듈명은 `Domain/Module` 형식으로 하는게 좋다.
- 모듈의 명명규칙은 **소문자**, **한단어** 이다.

`go.mod` 파일 가장 위에 선언한다.

```
module cozyfex.com/sample1

go 1.20
```

{{< admonition type=tip title="GOPATH" open=true >}}
모듈의 `Root Path`의 패키지 이름은 `Module`과 같은 이름으로 하는게 좋다.
{{< /admonition >}}

----

## Package

- 패키지명은 해당 파일이 존재하는 디렉토리명으로 한다.
- 같은 경로에 있는 파일들의 패키지명은 같아야 한다.
- 패키지의 명명규칙은 **소문자**, **한단어** 이다.

----

## Import

- 패키지 단위로 `import` 가능하다.
- 같은 프로젝트 하위에 있는 `package`라도 `go.mod`에 선언된 `module`의 하위 `package`로 `import`하는 것이다.
- 외부 패키지를 사용하기 위해서는 `go.mod` 파일에 선언되어 있어야 한다.
- `go.mod` 파일의 기본 `require`는 `GOPATH` 경로에서 모듈을 찾는다.
- `go get`으로 설치 할 수 없는 모듈은 `require`와 `replace`를 이용해 `import` 할 수 있다.

### 로컬 모듈 import 하기

아래 2개의 모듈이 있다고 가정한다.

- [`cozyfex.com/sample1`](https://github.com/cozyfex/go-sample1)
- [`cozyfex.com/sample2`](https://github.com/cozyfex/go-sample2)

#### 시나리오

sample1에서 sample1 모듈의 패키지들을 사용하려고 하는 예제입니다.  
sample1, sample2 모두 로컬에 위치하고 있으며, sample1을 `go get`으로 설치 할 수 없는 상황입니다.

#### 해결책

sample1의 `go.mod` 파일에서 sample1 모듈의 경로를 `GOPATH`가 아닌 곳으로 `replace` 해준다.  
위의 긴 글들중, 가장 중요한 포인트는 아래의 내용입니다.

```
require cozyfex.com/sample0 v0.0.0
replace cozyfex.com/sample0 v0.0.0 => ../sample1
```

다른 모듈들과 마찬가지로 `require` 해 준 다음, `replace`를 사용해서 경로를 변경해줍니다.  
이 때, 경로는 절대경로, 상대경로 모두 사용 가능합니다.

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

##### 파일 내용

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

> 여기서 확인할 수 있듯이, 같은 프로젝트의 패키지를 `import` 할 때에도 `go.mod` 파일에 선언한 모듈 이름을 기반으로 `import` 한다.

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

##### 파일 내용

###### `go.mod`

```
module cozyfex.com/sample2

go 1.20

require cozyfex.com/sample1 v0.0.0

replace cozyfex.com/sample1 v0.0.0 => ../sample1
```

> 로컬에 있는 모듈을 `import` 하기 위해서 `require`와 `replace`를 사용하여 사용할 모듈명을 경로와 매칭시켜준다.

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
