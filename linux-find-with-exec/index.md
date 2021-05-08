# Linux find with exec


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



