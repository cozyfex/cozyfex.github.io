---
title: "Go Gin Gonic Split Routes"

date: 2021-04-17T00:12:00+0900

author: CozyFex

categories:

- go
- gin-gonic

tags:

- go
- gin-gonic
- route

keywords:

- go
- gin-gonic
- route

---
## Example for split routes gin-gonic
----

##### Go to source in github

[Example Source Link](https://github.com/cozyfex/route-sample)

##### Directory structure

<sub>Directory structure</sub>

```shell
.
├── go.mod
├── go.sum
├── main.go
├── post
│   └── routes.go
├── schedule
│   └── routes.go
└── user
    └── routes.go
```

##### Source

<sub>`/post/routes.go`</sub>

```go
package post

import "github.com/gin-gonic/gin"

func Routes(r *gin.Engine) {
	post := r.Group("/post")
	{
		post.GET("/1", func(c *gin.Context) {
			c.JSON(200, gin.H{
				"post_id": 1,
			})
		})
	}
}
```

The `post` is an app you can make.\
And each app have a `routes.go` file has `Routes` function.\
The `Routes` is received a parameter `r *gin.Engine` from `main.go`.\
Then each app make a `r.Group` as an url path.\
After that, you can make sub url of the `r.Group`.\
`post`는 당신이 만들 수 있는 app 입니다.\
각 app 은 `routes.go` 파일을 갖습니다. 그리고 그 파일 안에는 `Routes`라는 함수를 만듭니다.\
`Routes` 함수는 `r *gin.Engine` 을 `main.go` 로부터 파라메터로 받습니다.\
그리고 각 app 은 `r.Group` 이라는 url path 를 만듭니다.\
그리고 나서는 하위 `r.Group` 의 하위 url 을 만들 수 있습니다.

<sub>`/schedule/routes.go`</sub>

```go
package schedule

import "github.com/gin-gonic/gin"

func Routes(r *gin.Engine) {
	schedule := r.Group("/schedule")
	{
		schedule.GET("/day", func(c *gin.Context) {
			c.JSON(200, gin.H{
				"day": "today",
			})
		})
	}
}
```

<sub>`/user/routes.go`</sub>

```go
package user

import "github.com/gin-gonic/gin"

func Routes(r *gin.Engine) {
	user := r.Group("/user")
	{
		user.GET("/id", func(c *gin.Context) {
			c.JSON(200, gin.H{
				"username": "User ID",
			})
		})
	}
}
```

<sub>`/main.go`</sub>

```go
package main

import (
	"github.com/gin-gonic/gin"
	"route-sample/post"
	"route-sample/schedule"
	"route-sample/user"
)

func main() {
	r := gin.Default()

	post.Routes(r)
	user.Routes(r)
	schedule.Routes(r)

	err := r.Run()
	if err != nil {
		return
	}
}
```

You have to pass the parameter like `post.Routes(r)` to each app from `main.go`.\
`main.go` 에서 `post.Routes(r)` 처럼 각 app 에 파라메터를 너겨줘야 합니다.
