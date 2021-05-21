---
title: "Kubernetes TLS 인증서 상세정보"

date: 2021-05-20T12:27:18+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- tls
- certificate details

keywords:

- kubernetes
- security
- tls
- certificate details

---

## 설정 확인

### 어려운 방법

```shell
cat /etc/systemd/system/kube-apiserver.service
```

```shell
[Service]
ExecStart=/usr/local/bin/kube-apiserver \\
    --advertise-address=172.17.0.32 \\
    --allow-privileged=true \\
    --apiserver-count=3 \\
    --authorization-mode=Node,RBAC \\
    --bind-address=0.0.0.0 \\
    --client-ca-file=/var/lib/kubernetes/ca.pem \\
    --enable-swagger-ui=true \\
    --etcd-cafile=/var/lib/kubernetes/ca.pem \\
    --etcd-certfile=/var/lib/kubernetes/kubernetes.pem \\
    --etcd-keyfile=/var/lib/kubernetes/kubernetes-key.pem \\
    --event-ttl=1h \\
    --kubelet-certificate-authority=/var/lib/kubernetes/ca.pem \\
    --kubelet-client-key=/var/lib/kubernetes/kubernetes-key.pem \\
    --kubelet-https=true \\
    --service-node-port-range=30000-32767 \\
    --tls-cert-file=/var/lib/kubernetes/kubernetes.pem \\
    --tls-private-key-file=/var/lib/kubernetes/kubernetes-key.pem
    --v=2
```

### `kubeadm`

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

```yaml
...
...

spec:
  containers:
    - command:
        - kube-apiserver
        - --advertise-address=10.53.4.9
        - --allow-privileged=true
        - --authorization-mode=Node,RBAC
        - --client-ca-file=/etc/kubernetes/pki/ca.crt
        - --enable-admission-plugins=NodeRestriction
        - --enable-bootstrap-token-auth=true
        - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
        - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
        - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
        - --etcd-servers=https://127.0.0.1:2379
        - --insecure-port=0
        - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
        - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
        - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
        - --requestheader-allowed-names=front-proxy-client
        - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
        - --requestheader-extra-headers-prefix=X-Remote-Extra-
        - --requestheader-group-headers=X-Remote-Group
        - --requestheader-username-headers=X-Remote-User
        - --secure-port=6443
        - --service-account-issuer=https://kubernetes.default.svc.cluster.local
        - --service-account-key-file=/etc/kubernetes/pki/sa.pub
        - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
        - --service-cluster-ip-range=10.96.0.0/12
        - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
        - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key

...
...
```

## 인증서 상세 확인

### CRT 파일 위치 확인

#### `kube-apiserver.yaml`

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

```yaml
...
...

spec:
  containers:
    - command:
        - kube-apiserver
        - --advertise-address=10.53.4.9
        - --allow-privileged=true
        - --authorization-mode=Node,RBAC
        - --client-ca-file=/etc/kubernetes/pki/ca.crt
        - --enable-admission-plugins=NodeRestriction
        - --enable-bootstrap-token-auth=true
        - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
        - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
        - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
        - --etcd-servers=https://127.0.0.1:2379
        - --insecure-port=0
        - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
        - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
        - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
        - --requestheader-allowed-names=front-proxy-client
        - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
        - --requestheader-extra-headers-prefix=X-Remote-Extra-
        - --requestheader-group-headers=X-Remote-Group
        - --requestheader-username-headers=X-Remote-User
        - --secure-port=6443
        - --service-account-issuer=https://kubernetes.default.svc.cluster.local
        - --service-account-key-file=/etc/kubernetes/pki/sa.pub
        - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
        - --service-cluster-ip-range=10.96.0.0/12
        # 여기!
        - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
        - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key

...
...
```

### CRT 상세 확인

```shell
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
```

```shell
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 7176149795067879968 (0x6396ce37be036220)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: May 20 04:26:37 2021 GMT
            Not After : May 20 04:26:37 2022 GMT
        Subject: CN = kube-apiserver
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:ea:80:b3:8f:f9:2b:10:1f:ff:a0:69:0b:84:d5:
                    bb:ba:a1:a6:09:82:b1:bf:79:e8:35:92:76:63:a1:
                    82:88:a1:c5:63:cd:91:94:f3:60:a4:1e:78:de:24:
                    b8:e6:00:e9:28:f7:f4:96:72:c7:c5:b0:52:6e:8c:
                    ad:13:9b:09:a8:ab:a3:fc:69:77:a7:9c:e9:27:32:
                    88:8a:67:3b:75:a5:d8:c2:56:65:1d:29:47:da:f3:
                    a7:8e:27:95:12:b7:92:ff:0d:5a:bf:b4:32:88:f3:
                    e2:c1:06:8f:8d:6d:99:87:21:44:01:54:58:e2:b4:
                    43:af:1c:50:4f:f5:86:e8:de:bb:6c:62:0f:73:be:
                    b2:ab:bc:6c:ec:81:4a:5d:b3:5e:07:9f:f8:84:44:
                    cc:47:5e:77:21:74:e8:b6:e1:99:fe:99:c4:5c:3d:
                    2a:6f:1a:4f:c4:44:fd:4e:75:4c:cb:bd:5e:fd:e3:
                    de:3b:5c:16:01:52:2c:0f:2e:ce:eb:ce:4e:06:ca:
                    04:5d:31:d0:97:f5:5a:76:f5:f8:c1:68:a5:a1:13:
                    a0:be:cf:f4:2e:e9:ca:e5:e8:0c:c5:49:26:c7:44:
                    f0:9a:0d:9b:b0:d1:a4:35:a2:9d:cb:a2:f3:ef:70:
                    c3:8d:77:3c:a3:b7:f6:40:5d:82:ea:fd:b5:4d:1d:
                    a2:1d
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication
            X509v3 Authority Key Identifier: 
                keyid:76:8A:B9:CB:4F:B4:36:33:17:2C:C3:92:1C:5C:FB:05:29:B1:B4:3C

            X509v3 Subject Alternative Name: 
                DNS:controlplane, DNS:kubernetes, DNS:kubernetes.default, DNS:kubernetes.default.svc, DNS:kubernetes.default.svc.cluster.local, IP Address:10.96.0.1, IP Address:10.53.4.9
    Signature Algorithm: sha256WithRSAEncryption
         14:73:c7:8b:e7:dd:22:f5:98:02:b1:15:cf:13:66:a8:79:64:
         9d:eb:ba:fb:57:02:ac:08:b3:90:f1:35:e3:3b:07:15:ce:44:
         d8:15:d9:72:53:be:b4:be:12:a4:78:1c:52:02:1c:f1:67:63:
         0d:3e:5b:98:49:f7:68:e3:fc:c2:92:24:85:c8:58:59:2d:59:
         e7:65:cf:15:2d:fb:dd:50:5b:d8:28:04:60:39:00:2c:14:4a:
         ac:b7:1d:0e:f9:95:05:f6:3d:bc:2e:10:d2:7d:65:c2:17:e2:
         c7:bd:68:08:92:7f:61:a1:6a:fb:c8:94:b5:bd:5b:58:2c:03:
         d0:e8:cb:7a:f4:aa:a6:8b:56:4f:33:bb:7b:cf:c7:ae:53:e4:
         af:06:a5:2a:ca:d1:93:72:94:63:d8:fd:12:46:66:69:f1:1b:
         16:4a:b6:62:31:f6:09:0a:27:e7:1c:db:03:58:61:a1:19:33:
         45:b5:65:72:b6:11:91:64:ed:ac:9c:91:e5:21:2d:6d:bd:2c:
         7f:3f:a2:24:f0:e1:ec:e1:e7:33:c8:59:82:3b:22:90:3f:a8:
         67:00:f7:b8:61:bc:d0:53:26:04:16:f2:35:8b:9c:26:68:3a:
         08:10:00:40:48:0b:38:88:4a:d4:1d:f8:64:b8:75:db:0b:1e:
         3f:d7:54:02
```

### Service 로그 조사

```shell
journalctl -u etcd.service -l
```

### 쿠버네티스 로그 확인

```shell
kubectl logs etcd-master
```

### 도커 로그 확인

```shell
# Find Container ID
docker ps -a

# View Logs
docker logs <container-id>
```

