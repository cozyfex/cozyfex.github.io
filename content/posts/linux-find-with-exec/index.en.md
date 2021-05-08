---
title: "Linux find with exec"

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

### find command

<sub>find command examples</sub>

```shell
find . -type f -user username

find . -type f -group username
```

### find with -exec

<sub>find command with -exec</sub>

```shell
find . -type f -user username -exec chown anotheruser:anotheruser {} \;
```


