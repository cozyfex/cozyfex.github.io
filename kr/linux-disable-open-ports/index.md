# Linux Open Ports 비활성화


## Listening Ports 확인

```shell
netstat -an | grep -w LISTEN
```

## Service 와 Port

```shell
cat /etc/services | grep -w 53
```

