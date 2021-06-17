# SSL 비밀번호


## SSL Key 비밀번호 제거

```shell
openssl rsa -in password.key -out nopassword.key
```

## SSL Key 비밀번호 설정

```shell
openssl rsa -in nopassword.key -passout pass:'PASSWORD' -out password.key -des3
```
