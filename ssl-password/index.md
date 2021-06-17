# SSL Password


## Remove Password of SSL Key

```shell
openssl rsa -in password.key -out nopassword.key
```

## Set Password of SSL Key

```shell
openssl rsa -in nopassword.key -passout pass:'PASSWORD' -out password.key -des3
```
