---
title: "Docker Scan Images for Known Vulnerabilities"

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
The Common Vulnerabilities and Exposures system provides a reference-method for publicly known information-security vulnerabilities and exposures.  
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
A Simple and Comprehensive Vulnerability Scanner for Containers and other Artifacts, Suitable for CI.  
This is one of a tool for CVE scanner.  
{{</admonition>}}

### Installation

{{<admonition note Trivy true>}}  
Go to official [Installation](https://aquasecurity.github.io/trivy/v0.18.3/installation/)
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

## Best Practices

* Continuously rescan images
* Kubernetes Admission Controllers to scan images
* Private repository with pre-scanned images ready to go
* Integrate scanning into your CI/CD pipeline

