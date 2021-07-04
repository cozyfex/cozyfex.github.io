---
title: "Docker Service Configuration"

date: 2021-07-04T18:14:28+09:00

author: CozyFex

categories:

- docker

tags:

- docker
- kubernetes
- service
- configuration

keywords:

- docker
- kubernetes
- service
- configuration

---

## Check Service Status

```shell
systemctl start docker
```

```shell
systemctl status docker
```

```shell
systemctl stop docker
```

```shell
systemctl restart docker
```

## Start Manually

```shell
dockerd
```

## Start Manually With Debug

```shell
dockerd --debug
```

## Docker Using Unix Socket

```
/var/run/docker.sock
```

## Start Manually With Host

```shell
dockerd --debug --host=tcp:192.168.1.10:2375
```

### Docker CLI Client Configuration

```shell
export DOCKER_HOST="tcp://192.168.1.10:2375"
```

```shell
docker ps
```

## TLS Encryption

| Port | Description |  
|:-:|:-|  
| 2375 | Un-encrypted |  
| 2376 | Encrypted |

### Docker Run With TLS

```shell
dockerd --debug \\
        --host=tcp://192.168.1.10:2376 \\
        --tls=true \\
        --tlscert=/var/docker/server.pem \\
        --tlskey=/var/docker/serverkey.pem
```

### Docker CLI Client Configuration

```shell
export DOCKER_TLS=true
export DOCKER_HOST="tcp://192.168.1.10:2376"
```

```shell
docker ps

# When DOCKER_TLS is not set
docker --tls ps
```

## Daemon Configuration File

### `/etc/docker/daemon.json`

```json
{
  "debug": true,
  "hosts": [
    "tcp://192.168.1.10:2376"
  ],
  "tls": true,
  "tlscert": "/var/docker/server.pem",
  "tlskey": "/var/docker/serverkey.pem"
}
```

### Docker Command With Daemon Configuration File

```shell
dockerd
```


