# Linux Disable Open Ports


## Check Listening Ports

```shell
netstat -an | grep -w LISTEN
```

## Service with Port

```shell
cat /etc/services | grep -w 53
```

