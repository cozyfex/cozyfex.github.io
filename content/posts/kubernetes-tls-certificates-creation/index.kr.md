---
title: "Kubernetes TLS 인증서 생성"

date: 2021-05-19T19:59:26+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- tls
- certificates creation

keywords:

- kubernetes
- security
- tls
- certificates creation

---

## 인증서 생성 툴

{{<admonition note OpenSSL true>}}  
여기서는 OpenSSL 을 사용한다.  
{{</admonition>}}

- [Easy-RSA](https://github.com/OpenVPN/easy-rsa)
- [OpenSSL](https://www.openssl.org/)
- [CFSSL](https://github.com/cloudflare/cfssl)

## 생성 프로세스

### 키 생성

```shell
openssl genrsa -out ca.key 2048
```

### 인증서 서명 요청(CSR)

```shell
openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
```

### 인증서 서명

```shell
openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
```

## CA(인증 기관) 생성

### CA 키 생성

```shell
openssl genrsa -out ca.key 2048
```

### CA 인증서 서명 요청(CSR)

```shell
openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
```

### CA 인증서 서명

{{<admonition note Note true>}}  
`-signkey` is `'ca.key`.  
{{</admonition>}}

```shell
openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
```

## Admin User 생성

### Admin User 키 생성

```shell
openssl genrsa -out admin.key 2048
```

### Admin User 인증서 서명 요청(CSR)

```shell
openssl req -new -key admin.key -subj "/CN=kube-admin/OU=system:masters" -out admin.csr
```

### Admin User 인증서 서명

{{<admonition note Note true>}}  
`-CA` is `'ca.crt`.  
`-CAkey` is `'ca.key`.  
There's no `-signkey`.  
{{</admonition>}}

```shell
openssl x509 -req -in admin.csr –CA ca.crt -CAkey ca.key -out admin.crt
```

## 인증서 속성

| Key | Type | Kubernetes |   
|:-:|:-:|:-:|  
| <span style="color: yellow;">CN</span> | Common Name | kube-admin |  
| <span style="color: yellow;">OU</span> | Organization Unit | system:masters |  
| <span style="color: yellow;">O</span> | Organization | - |  
| <span style="color: yellow;">L</span> | Locality | - |  
| <span style="color: yellow;">S</span> | State or Province Name | - |  
| <span style="color: yellow;">C</span> | Country Name | - |

## Kubernetes 인증서 속성

| Component | Key | Value |  
|:-:|:-:|:-:|  
| Certificate Authority | CN | KUBERNETES-CA |  
| Admin User | CN | kube-admin |  
|| OU | system:masters |  
| KUBE SCHEDULER | CN | kube-scheduler |  
|| OU | system:kube-scheduler |  
| KUBE CONTROLLER MANGER | CN | kube-controller-manager |  
|| OU | system:kube-controller-manager |  
| KUBE PROXY | CN | kube-proxy |  
|| OU | kube-proxy |  
| ETCD SERVER | CN | etcd-server |

## Kubernetes 인증서 생성 파일

| Component | Key File | CSR File | CRT File |  
|:-:|:-:|:-:|:-:|  
| Certificate Authority | ca.key | ca.csr | ca.crt |  
| Admin User | admin.key | admin.csr | admin.crt |  
| KUBE SCHEDULER | scheduler.key | scheduler.csr | scheduler.crt |  
| KUBE CONTROLLER MANAGER | controller-manager.key | controller-manager.csr | controller-manager.crt |  
| KUBE PROXY | kube-proxy.key | kube-proxy.csr | kube-proxy.crt |

## Kubernetes 인증서 설정

| Component | Service File Location |  
|:-:|:-:|  
| KUBE API SERVER | /etc/kubernetes/manifests/kube-apiserver.yaml |  
| KUBE SCHEDULER | /etc/kubernetes/manifests/kube-scheduler.yaml |  
| KUBE CONTROLLER MANAGER | /etc/kubernetes/manifests/kube-controller-manager.yaml |  
| ETCD | /etc/kubernetes/manifests/etcd.yaml |

## 클라이언트를 위한 클라이언트 인증서

<sub>Curl</sub>

```shell
curl https://kube-apiserver:6443/api/v1/pods \
    --key admin.key --cert admin.crt \
    --cacert ca.crt
```

<sub>Result</sub>

```json
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "selfLink": "/api/v1/pods"
  },
  "items": []
}
```

<sub>kube-config.yaml</sub>

```yaml
apiVersion: v1
clusters:
  - cluster:
      certificate-authority: ca.crt
      server: https://kube-apiserver:6443
    name: kubernetes
kind: Config
users:
  - name: kubernetes-admin
    user:
      client-certificate: admin.crt
      client-key: admin.key
```

## ETCD SERVERS

### 인증서

#### ETCD-SERVER

#### ETCD-PEER

### `etcd.yaml`

```shell
- etcd
    - --advertise-client-urls=https://127.0.0.1:2379
    - --key-   -to-certs/
    - --cert-   -to-
    - --client-cert-auth=true
    - --data-dir=/var/lib/etcd
    - --initial-advertise-peer-urls=https://127.0.0.1:2380 - --initial-cluster=master=https://127.0.0.1:2380
    - --listen-client-urls=https://127.0.0.1:2379
    - --listen-peer-urls=https://127.0.0.1:2380
    - --name=master
    - --peer-cert- -to-
    - --peer-client-cert-
    - --peer-key-file=/etc/
    - --peer-trusted-ca-file=/etc/
    - --snapshot-count=10000
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

## KUBE API SERVER

### 인증서

#### KUBE-API SERVER

### 키 생성

```shell
openssl genrsa -out apiserver.key 2048
```

### CSR(인증서 서명 요청)

```shell
# No option
openssl req -new -key apiserrver.key -subj "/CN=kube-apiserver" -out apiserver.csr

# With options
openssl req -new -key apiserrver.key -subj "/CN=kube-apiserver" -out apiserver.csr -config openssl.cnf 
```

<sub>openssl.cnf</sub>

```
[req]
req_extensions = v3_req
[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation,
subjectAltName = @alt_names
[alt_names]
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
DNS.4 = kubernetes.default.svc.cluster.local
IP.1 = 10.96.0.1
IP.2 = 172.17.0.87
```

### CRT(인증서)

```shell
openssl x509 -req -in apiserver.csr -CA ca.crt -CAkey ca.key -out apiserver.crt
```

## KUBECTL NODES(SERVER CERT)

{{<admonition note "Node CERT">}}  
각 노드의 `CERTIFICATE` 그룹은 `Node Name` 이다.  
아래 예제에서는, `node01` 이다.  
{{</admonition>}}

### `kubelet-config.yaml`(node01)

```yaml
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  x509:
    clientCAFile: "/var/lib/kubernetes/ca.pem"
authorization:
  mode: Webhook
clusterDomain: "cluster.local"
clusterDNS:
  - "10.32.0.10"
podCIDR: "${POD_CIDR}"
resolvConf: "/run/systemd/resolve/resolv.conf"
runtimeRequestTimeout: "15m"
tlsCertFile: "/var/lib/kubelet/kubelet-node01.crt"
tlsPrivateKeyFile: "/var/lib/kubelet/kubelet-node01.key"
```


