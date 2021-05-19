---
title: "Kubernetes TLS Certificates Basic"

date: 2021-05-19T14:23:59+09:00

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

## Symmetric Encryption

### Plain Text Transport

#### Data

```yaml
User: John
Password: Pass123
```

{{<mermaid>}}

graph LR;

U(User) -->|Data| S(Server)

{{</mermaid>}}

{{<admonition note "Plain Text" true>}}  
This is very weak for security.  
There's no encryption.  
It's easy to hijack the `data`.  
{{</admonition>}}

### Encryption Transport

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

{{<admonition note "Symmetric Encryption" true>}}  
This is encrypted, but it's still weak for security.  
It's still easy to hijack `data` including the `key`.  
So they can decrypt the `data` with `key`.  
{{</admonition>}}

## Asymmetric Encryption

### Mechanism

{{<admonition note Theory true>}}  
Split the `key` to `Public Key` and `Private Key`.  
`Public Key` is used to encrypt, `Private Key` is used to decrypt.  
So it's an easy way to remember `Public Key` as `Public Lock`.  
{{</admonition>}}

### SSH

#### Generate Client User's `Private Key` and `Public Key`

```shell
ssh-keygen

id_rsa # Private Key
id_rsa.pub # Public Key
```

#### Register User's `id_rsa.pub` to the Server

```shell
vi ~/.ssh/authorized_keys
```

<sub>Public Key List</sub>

```shell
ssh-rsa AAAAB3NzaC1yc...KhtUBfoTzlBqRV1NThvOo4opzEwRQo1mWx user1
ssh-rsa AAAXCVJSDFDF...SLKJSDLKFw23423xckjSDFDFLKJLSDFKJLx user2
```

#### Connect to the Server

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

### Personal Asymmetric Encryption in Web

{{<admonition note "Personal Problem" true>}}  
It can be hijacked by DNS spoofing and copy the website that has their RSA.  
{{</admonition>}}

#### Generate RSA

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

### CA(Certificate Authority)

{{<admonition note "Certificate Authority" true>}}  
When you use personal RSA asymmetric encryption, there's security problems that I talked above.  
For security, you need to use certificate authority services.  
{{</admonition>}}

{{<admonition note "CSR" true>}}  
Certificate Signing Request  
{{</admonition>}}

{{<admonition note "Browser and CA" true>}}  
All browsers are built in certificate validation mechanism.  
The browsers have public keys of well known certificate authority organizations.  
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

### PKI(Public Key Infrastructure)

{{<admonition note PKI true>}}  
Above whole structure is called PKI.  
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

