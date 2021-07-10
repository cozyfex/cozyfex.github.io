---
title: "Kubernetes 보안 기초 요소"

date: 2021-05-19T11:54:20+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- security primitives

keywords:

- kubernetes
- security
- security primitives

---

## Secure Hosts

- 암포 기반 인증 미사용
- SSH Key 기반 인증

## Secure Kubernetes - `kube-apiserver`

### 인증 - 누가 접근 하는가?

- Files – Username and Passwords
- Files – Username and Tokens
- Certificates
- External Authentication providers - LDAP
- Service Accounts

### 인가(권한 부여) - 접근 후 무엇을 할 수 있는가?

- RBAC Authorization
- ABAC Authorization
- Node Authorization
- Webhook Mode

### TLS 증명

{{<admonition note TLS true>}}  
`TLS`(Transport Layer Security) 는 암호화 통신 프로토콜이다.  
이 기술은 넷스케이프 커뮤니케이션스에서 개발한 SSL(Secure Sockets Layer) 에 기반한 기술이다.  
이것은 IETF(Internet Engineering Task Force) 에서 인증받은 표준 프로토콜이다.  
아직까지도 SSL(Secure Sockets Layer) 라고 불리운다.  
{{</admonition>}}

### Network 정책