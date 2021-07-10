---
title: "Kubernetes TLS 인증 기초"

date: 2021-05-19T14:23:59+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- tls
- certificate

keywords:

- kubernetes
- security
- tls
- certificate

---

## 대칭 암호화

### 일반 텍스트 전송

#### Data

```yaml
User: John
Password: Pass123
```

{{<mermaid>}}

graph LR;

U(User) -->|Data| S(Server)

{{</mermaid>}}

{{<admonition note "일반 텍스트" true>}}  
이 방법은 보안상 매우 취약하다.  
암호화를 하지 않는다.  
`data` 를 가로채기 쉽다.  
{{</admonition>}}

### 암호화 전송

#### Data

```yaml
User: John
Password: Pass123
```

#### Key

```
XKSDJ39K34KJSDF0934JHSDFSDF3DKSDG
```

#### Encrypted Data

```yaml
XCVB: DKSJD
LKJSDFK: XZKJSDFL
```

{{<mermaid>}}

graph LR;

U(User) -->|Key and Encrypted Data| S(Server)

{{</mermaid>}}

{{<admonition note "대칭 암호화" true>}}  
암호화가 되지만, 아직 보안에 취약하다.  
여전히 `data` 와 `key` 를 가로채기 쉽다.  
가로챈 후 `key` 로 `data` 를 복호화 한다.  
{{</admonition>}}

## 비대칭 암호화

### 메커니즘

{{<admonition note Theory true>}}  
`key` 를 `Public Key` 와 `Private Key` 로 나눈다.  
`Public Key` 는 암호화에, `Private Key` 는 복호화에 사용된다.  
쉽게 외우는 방법은 `Public Key` 를 `Public Lock`으로 기억하는 것이다.  
{{</admonition>}}

### SSH

#### 클라이언트 사용자의 `Private Key` 와 `Public Key` 생성

```shell
ssh-keygen

id_rsa # Private Key
id_rsa.pub # Public Key
```

#### 사용자의 `id_rsa.pub` 를 서버에 등록

```shell
vi ~/.ssh/authorized_keys
```

<sub>Public Key List</sub>

```shell
ssh-rsa AAAAB3NzaC1yc...KhtUBfoTzlBqRV1NThvOo4opzEwRQo1mWx user1
ssh-rsa AAAXCVJSDFDF...SLKJSDLKFw23423xckjSDFDFLKJLSDFKJLx user2
```

#### Server 에 연결

```shell
ssh -i id_rsa user1@server
```

#### Process Summary

{{<mermaid>}}

graph TD;

G['ssh-keygen' in Client] --> PBK(Public Key as 'id_rsa.pub')

G --> PVK(Private Key as 'id_rsa')

PBK -->|Register Public Key to the Server| R("vi ~/.ssh/authorized_keys in Server")

C("ssh -i id_rsa user1@server") -->|Match authorized_keys with id_rsa| S(Server)

{{</mermaid>}}

### 웹에서 개인의 비대칭 암호화

{{<admonition note "개인화의 문제점" true>}}  
DNS 스푸핑과 자신만의 비대칭 암호화를 가진 복제된 웹사이트에 의해 데이터를 가로채기 당할 수 있다.  
{{</admonition>}}

#### RSA 생성

<sub>Private Key</sub>

```shell
openssl genrsa -out private.key 1024
```

<sub>Public Key</sub>

```shell
openssl rsa -in private.key -pubout > public.pem
```

#### Process

{{<mermaid>}}

sequenceDiagram

Client->>Server: First Access

activate Server

Server->>Client: Public Key

participant Client

Note left of Client: Encrypt Data with Public Key

Client->>Server: Encrypted Data

participant Server

Note right of Server: Decrypt with Private Key of the Encrypted Data

Server->>Client: Response

deactivate Server

{{</mermaid>}}

### CA(인증 기관)

{{<admonition note "인증 기관" true>}}  
개인 RSA 비대칭 암호화를 사용 할 때, 위에서 언급한 보안 문제가 발생 할 수 있다.  
보안상의 이유로, 인증 기관 서비스를 이용해야 할 필요가 있다.  
{{</admonition>}}

{{<admonition note "CSR" true>}}  
인증서 서명 요청  
{{</admonition>}}

{{<admonition note "Browser and CA" true>}}  
모든 브라우저에는 인증서 유효성 검사 메커니즘이 내장되어 있습니다.  
브라우저에는 잘 알려진 인증 기관 조직의 공개 키가 있습니다.  
{{</admonition>}}

```shell
openssl req -new -key private.key -out private.csr -subj "/C=US/ST=CA/O=MyOrg./CN=my-domain.com"
```

<sub>private.csr</sub>

```shell
-----BEGIN CERTIFICATE REQUEST-----
MIICjDCCAXQCAQAwRzELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAkNBMRQwEgYDVQQK
DAtNeU9yZywgSW5jLjEVMBMGA1UEAwwMbXlkb21haW4uY29tMIIBIjANBgkqhkiG
....
mDJep5YY92JxtAKZZt52wsj8MeUwTUjn6eDuz5NhpoKuiWMf9LoxGFYrgAGi2x1o
Fkse6Zr6zaB/cNdm6daW8m6qVs9hKpudTiqgD3g4MEuLLPK7VNxfFTMoSIfkLUui
-----END CERTIFICATE REQUEST-----
```

#### Process

{{<mermaid>}}

sequenceDiagram

Client->>Server: First Access

Server->>CA: Send CSR Public Key

CA->>Server: Response CERT when CSR is validated

activate Server

Server->>Client: Response CERT

Client->>Server: Encrypted Data with Client Certificate

Server->>Client: Response when it's validated

deactivate Server

{{</mermaid>}}

### PKI(공개 키 인프라)

{{<admonition note PKI true>}}  
위에서 말한 모든 구조를 PKI 라고 한다.  
{{</admonition>}}

|| Certificate(Public Key) | Private Key |  
|:-:|:-:|:-:|  
| Purpose | Encrypt(Lock) | Decrypt |  
| Extension Type | *.crt | *.key |  
|| *.pem | *-key.pem |  
| Examples | server.crt | server.key |  
|| server.pem | server-key.pem |  
|| client.crt | client.key |  
|| client.pem | client-key.pem |  

