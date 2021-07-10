---
title: "Docker 최소화 Base Image"

date: 2021-07-08T21:12:46+09:00

author: CozyFex

categories:

- docker

tags:

- docker
- base image
- parent image

keywords:

- docker
- base image
- parent image

---

## Base 와 Parent Image

| Type | Description |  
|:-:|:-|  
| Base Image | 베이스 이미지는 Dockerfile 에 FROM scratch 를 가지고 있는 이미지를 말한다. |  
| Parent Image | 부모 이미지는 당신의 이미지의 베이스가 되는 이미지이다. |

## Base Image 선택 규칙

### 확실성

{{<admonition note Authenticity true>}}  
도커 허브에서 `OFFICIAL IMAGE` 를 확인한다.  
{{</admonition>}}

### 최신정보

{{<admonition note Up-to-date true>}}  
도커 허브에서 업데이트 시간을 확인한다.  
{{</admonition>}}

### Slim and Minimal Image

* 오직 필요한 패키지만 설치
    * Shells, Package Managers, Tools 등을 삭제
* 각각 환경별로 이미지 관리
    * 개발 - 디버그 툴 포함
    * 프로덕션 - 군더더기 없는
* 군더더기 없는 프로덕션 준비 이미지 생성을 위한 멀티스테이지 빌드 사용
* Use multi-stage builds to create lean production ready images

### Distroless Docker Images

* 포함
    * 어플리케이션
    * 런다임 의존성
* 미포함
    * 패키지 매니저
    * 쉘
    * 네트워크 툴
    * 텍스트 에디터
    * 기타

### 취약점 스캔

```shell
trivy image httpd
```

```shell
trivy image httpd:alpine
```