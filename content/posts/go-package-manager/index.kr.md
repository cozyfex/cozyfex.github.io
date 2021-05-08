---
title: "Go 패키지 메니저"

date: 2021-04-17T00:09:00+0900

author: CozyFex

categories:

- go

tags:

- go
- package manager

keywords:

- go
- package manager

---
## Go 패키지 메니저
----

##### 초기화

<sub>Go init</sub>

```shell
go init mod [Package Name]
```

이 명령은 `go.mod` 파일을 생성한다.

##### 생성 요구사항

<sub>Go 요구사항</sub>

```shell
go mod tidy
```

이 명령은 `go.mod` 에 추가하고 `go.sum`를 생성한다.



