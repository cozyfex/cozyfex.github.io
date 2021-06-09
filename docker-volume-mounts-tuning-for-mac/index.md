# Docker Volume Mounts Tuning for Mac


## Docker Volume Mount Performance Issue on Mac

{{<admonition note Issue true>}}  
There's a performance issue on Mac.  
In my case, the `docker.hyperkit` process is using high cpu resources.  
You'd better use `cached` configuration in conclusion.  
{{</admonition>}}

## Tuning Configuration

| Configuration | Description |  
|:-:|:-|  
| `delegated` | The container's view of file system writes performed by containers my not be immediately reflect on the host file system. |  
| `cached` | The `cached` configuration provides all the guarantees of the `delegated` configuration, and some additional guarantees around the visibility of writes performed by containers. |  
| `consistent` | This is default. The container and host are always synchronizing. |

## Examples

### Volume Mount with Default Configuration as `consistent`

```shell
docker run -v /host/data/:/var/lib/mysql mariadb:latest
```

### Volume Mount with `delegated`

```shell
docker run -v /host/data/:/var/lib/mysql:delegated mariadb:latest
```

### Volume Mount with `cached`

```shell
docker run -v /host/data/:/var/lib/mysql:cached mariadb:latest
```

