---
title: "Linux OPA"

date: 2021-07-06T20:18:48+09:00

author: CozyFex

categories:

- linux
- opa
- open policy agent

tags:

- linux
- opa
- open policy agent

keywords:

- linux
- opa
- open policy agent

---

## OPA

{{<admonition note OPA true>}}  
Open Policy Agent 의 약자이다.  
클라우드 네이티브 환경을 위한 정책 기반 제어.  
[Official](https://www.openpolicyagent.org)
{{</admonition>}}

## Installation

```shell
export OPA_VERSION=v0.27.1
curl -L -o opa https://github.com/open-policy-agent/opa/releases/download/${OPA_VERSION}/opa_linux_amd64
chmod 755 ./opa
```

```shell
./opa run -s &
```

## Test Policy

```shell
./opa test <rego-file-name>
```

## View Policies

```shell
curl http://localhost:8181/v1/policies
```

## Load Policy

```shell
# Structure
curl -X PUT --data-binary @<rego-policy-file-name> http://localhost:8181/v1/policies/<policy-name>
```

```shell
# Example
curl -X PUT --data-binary @sample.rego http://localhost:8181/v1/policies/samplepolicy
```

## Unload Policy

```shell
curl -X DELETE http://localhost:8181/v1/policies/<policy-name>
```

```shell
curl -X DELETE http://localhost:8181/v1/policies/samplepolicy
```

## OPA Default Information

| Default Port | Language |  
|:-:|:-:|  
| 8181 | Rego |  



