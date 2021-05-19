---
title: "Kubernetes Security Primitives"

date: 2021-05-19T11:54:20+09:00

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

- Password based authentication disabled
- SSH Key based authentication

## Secure Kubernetes - `kube-apiserver`

### Authentication - Who Can Access?

- Files – Username and Passwords
- Files – Username and Tokens
- Certificates
- External Authentication providers - LDAP
- Service Accounts

### Authorization - What Can They Do?

- RBAC Authorization
- ABAC Authorization
- Node Authorization
- Webhook Mode

### TLS Certificates

{{<admonition note TLS true>}}  
`TLS` stand for Transport Layer Security.  
It's a technology based on SSL(Secure Sockets Layer) that is developed by Netscape Communications.  
This is standard protocol that is approved by IETF(Internet Engineering Task Force).  
It's still used calling SSL.  
{{</admonition>}}

### Network Policies