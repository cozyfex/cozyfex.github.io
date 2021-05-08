# Linux find 후 명령 실행


## find 명령

<sub>find command examples</sub>

```shell
find . -type f -user username

find . -type f -group username
```

## find와 -exec 옵션

<sub>find command with -exec</sub>

```shell
find . -type f -user username -exec chown anotheruser:anotheruser {} \;
```



