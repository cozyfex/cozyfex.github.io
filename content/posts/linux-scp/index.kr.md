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
`scp` 는 `Secure Copy` 의 약자이다.  
`ssh` 를 베이스로 한다.  
{{</admonition>}}

## Command

### 기초

```shell
scp <from> <to>
```

### 원격

```shell
# File
root@1.2.3.4:abc.txt

# Directory
root@1.2.3.4:~/temp

# Multiple Files
root@1.2.3.4:"abc.txt efg.txt xyz.txt"
```

## 예제

### 로컬에서 원격으로

#### 단일 파일

```shell
scp test1.txt root@1.2.3.4:~/
```

#### 다중 파일

```shell
scp test1.txt test2.txt test3.txt root@1.2.3.4:~/
```

#### 디렉토리

```shell
scp ~/temp root@1.2.3.4:~/
```

### 원격에서 로컬로

#### 단일 파일

```shell
scp root@1.2.3.4:/home/test/test1.txt ~/
```

#### 다중 파일

```shell
scp root@1.2.3.4:"/home/test/test1.txt /home/test/test2.txt" ~/
```

#### 디렉토리

```shell
scp root@1.2.3.4:/home/test ~/
```
