---
title: "Linux scp"

date: 2021-07-27T19:53:13+09:00

author: CozyFex

categories:

- linux
- scp
- remote copy

tags:

- linux
- scp
- remote copy

keywords:

- linux
- scp
- remote copy

---

## scp

{{<admonition note scp true>}}  
`scp` stand for `Secure Copy`
This is based on `ssh`
{{</admonition>}}

## Command

### Basic

```shell
scp <from> <to>
```

### Remote

```shell
# File
root@1.2.3.4:abc.txt

# Directory
root@1.2.3.4:~/temp

# Multiple Files
root@1.2.3.4:"abc.txt efg.txt xyz.txt"
```

## Examples

### Local to Remote

#### Single File

```shell
scp test1.txt root@1.2.3.4:~/
```

#### Multiple Files

```shell
scp test1.txt test2.txt test3.txt root@1.2.3.4:~/
```

#### Directory

```shell
scp ~/temp root@1.2.3.4:~/
```

### Remote to Local

#### Single File

```shell
scp root@1.2.3.4:/home/test/test1.txt ~/
```

#### Multiple Files

```shell
scp root@1.2.3.4:"/home/test/test1.txt /home/test/test2.txt" ~/
```

#### Directory

```shell
scp root@1.2.3.4:/home/test ~/
```
