# Linux Kernel Modules


## Load Module

```shell
modprobe pcspkr
```

## Module List

```shell
lsmod
```

## Blacklist Modules

```shell
vi /etc/modprobe.d/blacklist.conf
```

```
blacklist sctp
blacklist dccp
```

```shell
shutdown -r now
```

```shell
lsmod | grep sctp
lsmod | grep dccp
```


