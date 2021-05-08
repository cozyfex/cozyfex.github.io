---
title: "Linux find 후 명령 실행"

date: 2021-04-15T11:00:00+0900

author: CozyFex

categories:

- linux

tags:

- linux
- find
- exec
- command

keywords:

- linux
- find
- exec
- command

---

### find 명령

<sub>find command examples</sub>

```shell
find . -type f -user username

find . -type f -group username
```

### find와 -exec 옵션

<sub>find command with -exec</sub>

```shell
find . -type f -user username -exec chown anotheruser:anotheruser {} \;
```


