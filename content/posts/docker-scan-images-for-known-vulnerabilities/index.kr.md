---
title: "Docker 알려진 취약점 확인을 위한 이미지 스캔"

date: 2021-07-08T23:45:49+09:00

author: CozyFex

categories:

- docker
- cve
- trivy

tags:

- docker
- cve
- trivy
- image
- vulnerabilities

keywords:

- docker
- cve
- trivy
- image
- vulnerabilities

---

## CVE(Common Vulnerabilities and Exposures)

{{<admonition note CVE true>}}  
공통 취약성 및 노출 시스템은 공개적으로 알려진 정보 보안 취약성 및 노출에 대한 참조 방법을 제공합니다.  
{{</admonition>}}

## CVE Severity Scores

### CVSS v2.0 Ratings

| Severity | Base Score Range |  
|:-:|:-:|  
| Low | 0.0-3.9 |  
| Medium | 4.0-6.9 |  
| High | 7.0-10.0 |

### CVSS v3.0 Ratings

| Severity | Base Score Range |  
|:-:|:-:|  
| None | 0.0 |  
| Low | 0.1-3.9 |  
| Medium | 4.0-6.9 |  
| High | 7.0-8.9 |  
| Critical | 9.0-10.0 |

## Trivy

{{<admonition note Trivy true>}}  
간단하고 종합적인 취약점 스캐너.  
CVE 스캐너중 하나이다.  
{{</admonition>}}

### Installation

{{<admonition note Trivy true>}}  
공식 문서 [Installation](https://aquasecurity.github.io/trivy/v0.18.3/installation/)
{{</admonition>}}

### Basic Usage

```shell
trivy image nginx:1.18.0
```

```shell
trivy image --severity CRITICAL nginx:1.18.0
```

```shell
trivy image --severity CRITICAL,HIGH nginx:1.18.0
```

```shell
trivy image --ignore-unfixed nginx:1.18.0
```

```shell
docker save nginx:1.18.0 > nginx.tar

trivy image --input nginx.tar
```

## 모범 사례

* 지속적인 이미지 재스캔
* Kubernetes Admission Controllers 통한 이미지 스캔
* 개인 저장소에 스캐닝 된 준비 이미지 사용
* CI/CD 파이프라인에 통합 스캐팅 제공

