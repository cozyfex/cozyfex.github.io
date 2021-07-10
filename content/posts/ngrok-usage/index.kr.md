---
title: "ngrok Usage"

date: 2021-05-25T16:25:42+09:00

author: CozyFex

categories:

- ngrok
- local
- test
- usage

tags:

- ngrok
- local
- test
- usage

keywords:

- ngrok
- local
- test
- usage

---

## ngrok 란?

{{<admonition note ngrok true>}}  
어떤 NAT 또는 방화벽을 통해 로컬 호스트 서버로 즉시, 보안 URL을 위한 하나의 명령  
Go to [ngrok](https://ngrok.com/)  
{{</admonition>}}

## ngrok 설치

### Download

{{<admonition note Download true>}}  
공식 사이트에서 `ngrok` 를 다운로드 하자  
{{</admonition>}}

### `ngrok` 파일 이동

```shell
# Create directories
mkdir ~/ngrok
mkdir ~/ngrok/bin

# Move
mv ~/Downloads/ngrok ~/ngrok/bin/

# Create a symbolic link
ln -s ~/ngrok/bin/ngrok /usr/local/bin/ngrok
```

### 계정 연결

1. `가입` 또는 `로그인`
2. `Getting Started` 아래의 `Setup & Installation` 로 이동
3. `authtoken` 확인
4. 아래 명령어 실행

```shell
ngrok authtoken <your-authtoken>
```

## Listen Http

```shell
ngrok http 80
```

```shell
ngrok by @inconshreveable

Session Status                online
Account                       <your-account>
Version                       2.3.40
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://3181a7385cf5.ngrok.io -> http://localhost:80
Forwarding                    https://3181a7385cf5.ngrok.io -> http://localhost:80

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

