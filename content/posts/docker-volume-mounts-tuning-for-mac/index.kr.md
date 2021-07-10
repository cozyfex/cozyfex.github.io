---
title: "Docker Volume Mounts Tuning for Mac"

date: 2021-06-05T07:31:39+09:00

author: CozyFex

categories:

- docker
- mac
- tuning

tags:

- docker
- tuning
- mac
- volume
- mount
- consistent
- cached
- delegated

keywords:

- docker
- tuning
- mac
- volume
- mount
- consistent
- cached
- delegated

---

## Mac 에서 Docker 볼륨 마운트 성능 이슈

{{<admonition note Issue true>}}  
맥에서 성능 이슈가 있다.  
내 경우에는 `docker.hyperkit` 프로세스가 높은 CPU 자원을 사용한다.  
결론부터 말하자면, `cached` 설정을 사용하길 추천한다.  
{{</admonition>}}

## 설정 튜닝

| Configuration | Description |  
|:-:|:-|  
| `delegated` | 컨테이너 파일 시스템에 먼저 쓰고, 호스트 파일 시스템에 즉시 반영되지 않는다. |  
| `cached` | `cached` 설정은 모든 `delegated` 설정을 제공한다. 그리고 보여지는것과 관련된 몇가지 추가기능을 제공한다. |  
| `consistent` | 기본 설정이다. 컨테이너와 호스트가 항상 동기화 된다. |

## Examples

### `consistent` 볼륨 마운트 - 기본 설정

```shell
docker run -v /host/data/:/var/lib/mysql mariadb:latest
```

### `delegated` 볼륨 마운트

```shell
docker run -v /host/data/:/var/lib/mysql:delegated mariadb:latest
```

### `cached` 볼륨 마운트

```shell
docker run -v /host/data/:/var/lib/mysql:cached mariadb:latest
```
