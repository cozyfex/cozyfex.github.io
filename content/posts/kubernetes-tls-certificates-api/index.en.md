---
title: "Kubernetes TLS Certificates API"

date: 2021-05-21T15:06:16+09:00

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
- api

keywords:

- kubernetes
- security
- tls
- certificate
- api

---

## Add New User to the Cluster

### Create a Key

```shell
openssl genrsa -out jane.key 2048
```

```shell
vi jane.key
```

<sub>jane.key</sub>

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvx/5t/OHmC9vHTBGAiyfXJ5W1rQ1Qf5yNMFoLLneh3mDrPVO
n7YfOMdDjv0UKvXMZE8H8po00v2bARCV4+m9VOrB3gL/ktpxyCsDd3P6TIDqh2eA
ARrNCkASpgMLoxsVhZDZ+oeTAthRF34AotlEPi6gBShi6/Rf+hU0/26s+mJCLRxy
jFwOjjJX3nZhO51FBnpTAXjSDQsDK+N3g6VvhIFhmSFz+aha4uI11fvX+9mkK6M/
IZPAZ8oAOqBT4pzqHOctkTqm3d6jmfpKcP3BX134S5ujKnG1WFhK3GKTg1yyA64N
9hM9YIfy5O8m4f9Vmhgzbjr0BI91BOxk9WHyxQIDAQABAoIBACF25M+LqXtBGvB+
alOpIoIiC0xuo0CZ379S+f2S0d7grOiUzx5x0gesBC0+LNDU1uPdXqPg0kz7MK/A
Cq6sV4VN0YIg/kTlULTeqy24NeQ9UlNBEQ2gdrRLwbglolz9ugf38+LE6D3ASHqX
il75MdS9bzFX//IWiNON8QikhU8Vpb+TBRh2N3zGSvzHzVkWRspTVtG8/VrtwokX
MM2PmyX1/9J0jTUCvmR9FkO2LCHQa+eb2Sn3YFkHhzlPKhM1x8xdsSPCnfR7gvI0
LpiVyGqwaIFepHduiu5n8gOeqvMzkaNYQf3uYGDmOaJjXFlU18JH+xmwoO9pLQ3/
aXxYNyECgYEA5pYJ2wrOzRmbS7fkMTm93t1VZwoOWMbv4Djb6gfR5W5DGERoJlOx
0FZ6T3sIxfREnbaYDpuZwO9PucCTmdHvyxOwE3fUMiXLxSNeSBbU1red6dUKuKqw
hBMFp9tCRC7rakQLMslTOWd2JH50p4Ca/6mK1c0neul1U5UkTAhIWOkCgYEA1DCL
P41B2eVB2nGaJiveBMRTRwJfw112GpB5CvUGJL6+H7x0PbT0v1l8NPSmuzOs5wuA
69CLcrB/zWhOG2R+6xi1lbSROrZj3WBi7InwI6Uqjpz1C0rKwU0cjKj9LqZIs/7c
/KJ355yPuxEUDw9EUdqx4K6I35Jo+90hucKEoX0CgYAjZ+BliNhON+NXkgnw4Qvd
f6DQbIPdb9Z/WFu0KLU7BF47CK3xTKKd+dlYAO1M7//lM5dPjubh8B0OyZogYVLv
nLtJkfm8JX+Paqncad0Ohy0xLWwzBMqLzRvuNUWMkKPHMmGBz9X+l3tViTFCUwX5
wb+2qoSoY9aHGfxzzEXHmQKBgAHk4QlTMHhKdJkHlJGsqJGwUvH6UnKC/g/kpD/1
yfVAAqBz5TG70Md4fARY/rgDERMK5j60oNoIO0vRF4llD0D3+n74mqdEg4WxW5Cy
9oLK8aNOQV21cDPjhVK4G3J0J4f7yWsz5BAcNKg90ckEoN5+LkJMLIqaWveMlMV8
uARdAoGAGmTOlpMYRTvjjPs5ojMCCDLJzKTrssfPa4kcWzjl1EjjDpESICzKPXC0
/rKxy5SBg/Pp/uS6lDdGqyBjLD/vIayxW+gMtCGqTbKa2Gs6zNSLV/qvtoByjoIa
zi6EgJ+DU50mTP+hU9AQa8awlA/MaKHDJZZJuMBaZDTzowYWxDI=
-----END RSA PRIVATE KEY-----
```

### Create a CSR(Certificate Signing Request)

```shell
openssl req -new -key jane.key -subj "/CN=jane" -out jane.csr
```

```shell
vi jane.csr
```

<sub>jane.csr</sub>

```
-----BEGIN CERTIFICATE REQUEST-----
MIICVDCCATwCAQAwDzENMAsGA1UEAwwEamFuZTCCASIwDQYJKoZIhvcNAQEBBQAD
ggEPADCCAQoCggEBAL8f+bfzh5gvbx0wRgIsn1yeVta0NUH+cjTBaCy53od5g6z1
Tp+2HzjHQ479FCr1zGRPB/KaNNL9mwEQlePpvVTqwd4C/5LaccgrA3dz+kyA6odn
gAEazQpAEqYDC6MbFYWQ2fqHkwLYURd+AKLZRD4uoAUoYuv0X/oVNP9urPpiQi0c
coxcDo4yV952YTudRQZ6UwF40g0LAyvjd4Olb4SBYZkhc/moWuLiNdX71/vZpCuj
PyGTwGfKADqgU+Kc6hznLZE6pt3eo5n6SnD9wV9d+EuboypxtVhYStxik4NcsgOu
DfYTPWCH8uTvJuH/VZoYM2469ASPdQTsZPVh8sUCAwEAAaAAMA0GCSqGSIb3DQEB
CwUAA4IBAQCGVWVLlmDCq22IeP9Tg2Tk7VH+l6As+zCAAysV6Qleim0h+ScTsvW6
Cq+UvdBJYYCeTuOZRboskH6VqDrYCMEmaKdG4Bl9B/XVGpsY9ZoW5c5ti7EEM3+f
hD1b6RcaOPI8j+fJfbxiOU1P22mBtyoPgVdRr97pSxnco2TvTV+9LvC4fFPyc/nl
POp9g+LTDMK7bH9GkqASUKhB06SL6UlioRu6r6Z19+CAFpN1X+HpW+L9fKcat+L7
w/AJFPDvmmxgpdUzVTgKDoteiGXOBFmna+veVgr0ESk6nWFTH/CbGNkkue+4FB7v
YfCgzSMt5Du7ccgUhZ0++nLEAcOR5unf
-----END CERTIFICATE REQUEST-----
```

### Base64 Encode CSR

```shell
cat jane.csr | base64 | tr -d '\n'

# or

cat jane.csr | base64 -w 0
```

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbUZ1WlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUw4ZitiZnpoNWd2Yngwd1JnSXNuMXllVnRhME5VSCtjalRCYUN5NTNvZDVnNnoxClRwKzJIempIUTQ3OUZDcjF6R1JQQi9LYU5OTDltd0VRbGVQcHZWVHF3ZDRDLzVMYWNjZ3JBM2R6K2t5QTZvZG4KZ0FFYXpRcEFFcVlEQzZNYkZZV1EyZnFIa3dMWVVSZCtBS0xaUkQ0dW9BVW9ZdXYwWC9vVk5QOXVyUHBpUWkwYwpjb3hjRG80eVY5NTJZVHVkUlFaNlV3RjQwZzBMQXl2amQ0T2xiNFNCWVpraGMvbW9XdUxpTmRYNzEvdlpwQ3VqClB5R1R3R2ZLQURxZ1UrS2M2aHpuTFpFNnB0M2VvNW42U25EOXdWOWQrRXVib3lweHRWaFlTdHhpazROY3NnT3UKRGZZVFBXQ0g4dVR2SnVIL1Zab1lNMjQ2OUFTUGRRVHNaUFZoOHNVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQ0dWV1ZMbG1EQ3EyMkllUDlUZzJUazdWSCtsNkFzK3pDQUF5c1Y2UWxlaW0waCtTY1Rzdlc2CkNxK1V2ZEJKWVlDZVR1T1pSYm9za0g2VnFEcllDTUVtYUtkRzRCbDlCL1hWR3BzWTlab1c1YzV0aTdFRU0zK2YKaEQxYjZSY2FPUEk4aitmSmZieGlPVTFQMjJtQnR5b1BnVmRScjk3cFN4bmNvMlR2VFYrOUx2QzRmRlB5Yy9ubApQT3A5ZytMVERNSzdiSDlHa3FBU1VLaEIwNlNMNlVsaW9SdTZyNloxOStDQUZwTjFYK0hwVytMOWZLY2F0K0w3CncvQUpGUER2bW14Z3BkVXpWVGdLRG90ZWlHWE9CRm1uYSt2ZVZncjBFU2s2bldGVEgvQ2JHTmtrdWUrNEZCN3YKWWZDZ3pTTXQ1RHU3Y2NnVWhaMCsrbkxFQWNPUjV1bmYKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
```

### jane-csr.yaml

```yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: jane
spec:
  groups:
    - system:authenticated
  usages:
    - digital signature
    - key encipherment
    - server auth
  request:
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbUZ1WlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUw4ZitiZnpoNWd2Yngwd1JnSXNuMXllVnRhME5VSCtjalRCYUN5NTNvZDVnNnoxClRwKzJIempIUTQ3OUZDcjF6R1JQQi9LYU5OTDltd0VRbGVQcHZWVHF3ZDRDLzVMYWNjZ3JBM2R6K2t5QTZvZG4KZ0FFYXpRcEFFcVlEQzZNYkZZV1EyZnFIa3dMWVVSZCtBS0xaUkQ0dW9BVW9ZdXYwWC9vVk5QOXVyUHBpUWkwYwpjb3hjRG80eVY5NTJZVHVkUlFaNlV3RjQwZzBMQXl2amQ0T2xiNFNCWVpraGMvbW9XdUxpTmRYNzEvdlpwQ3VqClB5R1R3R2ZLQURxZ1UrS2M2aHpuTFpFNnB0M2VvNW42U25EOXdWOWQrRXVib3lweHRWaFlTdHhpazROY3NnT3UKRGZZVFBXQ0g4dVR2SnVIL1Zab1lNMjQ2OUFTUGRRVHNaUFZoOHNVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQ0dWV1ZMbG1EQ3EyMkllUDlUZzJUazdWSCtsNkFzK3pDQUF5c1Y2UWxlaW0waCtTY1Rzdlc2CkNxK1V2ZEJKWVlDZVR1T1pSYm9za0g2VnFEcllDTUVtYUtkRzRCbDlCL1hWR3BzWTlab1c1YzV0aTdFRU0zK2YKaEQxYjZSY2FPUEk4aitmSmZieGlPVTFQMjJtQnR5b1BnVmRScjk3cFN4bmNvMlR2VFYrOUx2QzRmRlB5Yy9ubApQT3A5ZytMVERNSzdiSDlHa3FBU1VLaEIwNlNMNlVsaW9SdTZyNloxOStDQUZwTjFYK0hwVytMOWZLY2F0K0w3CncvQUpGUER2bW14Z3BkVXpWVGdLRG90ZWlHWE9CRm1uYSt2ZVZncjBFU2s2bldGVEgvQ2JHTmtrdWUrNEZCN3YKWWZDZ3pTTXQ1RHU3Y2NnVWhaMCsrbkxFQWNPUjV1bmYKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
```

### Create a Certificate Signing Request for Kubernetes

```shell
kubectl create -f jane-csr.yaml
```

### View CSR Pending Condition

```shell
kubectl get csr
```

```shell
NAME   AGE   SIGNERNAME                     REQUESTOR       CONDITION
jane   67s   kubernetes.io/legacy-unknown   minikube-user   Pending
```

### Certificate Approve or Deny

```shell
# Approve
kubectl certificate approve jane

# Deny
kubectl certificate deny jane
```

### View CSR Condition

```shell
kubectl get csr
```

```shell
NAME   AGE     SIGNERNAME                     REQUESTOR       CONDITION
jane   3m11s   kubernetes.io/legacy-unknown   minikube-user   Approved,Issued
```

### Delete CSR

```shell
kubectl delete csr jane
```

### Generate CSR Object YAML from exist User

```shell
kubectl get csr jane -o yaml > jane-exist-csr.yaml
```

<sub>jane-exist-csr.yaml</sub>

```yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  creationTimestamp: "2021-05-21T06:42:23Z"
  managedFields:
    - apiVersion: certificates.k8s.io/v1beta1
      fieldsType: FieldsV1
      fieldsV1:
        f:spec:
          f:groups: { }
          f:request: { }
          f:signerName: { }
          f:usages: { }
      manager: kubectl-create
      operation: Update
      time: "2021-05-21T06:42:23Z"
    - apiVersion: certificates.k8s.io/v1
      fieldsType: FieldsV1
      fieldsV1:
        f:status:
          f:certificate: { }
      manager: kube-controller-manager
      operation: Update
      time: "2021-05-21T06:43:44Z"
    - apiVersion: certificates.k8s.io/v1
      fieldsType: FieldsV1
      fieldsV1:
        f:status:
          f:conditions:
            .: { }
            k:{"type":"Approved"}:
              .: { }
              f:lastTransitionTime: { }
              f:lastUpdateTime: { }
              f:message: { }
              f:reason: { }
              f:status: { }
              f:type: { }
      manager: kubectl
      operation: Update
      time: "2021-05-21T06:43:44Z"
  name: jane
  resourceVersion: "29447"
  uid: 60c2556d-fe96-4f24-8435-6e62f20940b5
spec:
  groups:
    - system:masters
    - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbUZ1WlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUw4ZitiZnpoNWd2Yngwd1JnSXNuMXllVnRhME5VSCtjalRCYUN5NTNvZDVnNnoxClRwKzJIempIUTQ3OUZDcjF6R1JQQi9LYU5OTDltd0VRbGVQcHZWVHF3ZDRDLzVMYWNjZ3JBM2R6K2t5QTZvZG4KZ0FFYXpRcEFFcVlEQzZNYkZZV1EyZnFIa3dMWVVSZCtBS0xaUkQ0dW9BVW9ZdXYwWC9vVk5QOXVyUHBpUWkwYwpjb3hjRG80eVY5NTJZVHVkUlFaNlV3RjQwZzBMQXl2amQ0T2xiNFNCWVpraGMvbW9XdUxpTmRYNzEvdlpwQ3VqClB5R1R3R2ZLQURxZ1UrS2M2aHpuTFpFNnB0M2VvNW42U25EOXdWOWQrRXVib3lweHRWaFlTdHhpazROY3NnT3UKRGZZVFBXQ0g4dVR2SnVIL1Zab1lNMjQ2OUFTUGRRVHNaUFZoOHNVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQ0dWV1ZMbG1EQ3EyMkllUDlUZzJUazdWSCtsNkFzK3pDQUF5c1Y2UWxlaW0waCtTY1Rzdlc2CkNxK1V2ZEJKWVlDZVR1T1pSYm9za0g2VnFEcllDTUVtYUtkRzRCbDlCL1hWR3BzWTlab1c1YzV0aTdFRU0zK2YKaEQxYjZSY2FPUEk4aitmSmZieGlPVTFQMjJtQnR5b1BnVmRScjk3cFN4bmNvMlR2VFYrOUx2QzRmRlB5Yy9ubApQT3A5ZytMVERNSzdiSDlHa3FBU1VLaEIwNlNMNlVsaW9SdTZyNloxOStDQUZwTjFYK0hwVytMOWZLY2F0K0w3CncvQUpGUER2bW14Z3BkVXpWVGdLRG90ZWlHWE9CRm1uYSt2ZVZncjBFU2s2bldGVEgvQ2JHTmtrdWUrNEZCN3YKWWZDZ3pTTXQ1RHU3Y2NnVWhaMCsrbkxFQWNPUjV1bmYKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
  signerName: kubernetes.io/legacy-unknown
  usages:
    - digital signature
    - key encipherment
    - server auth
  username: minikube-user
status:
  certificate: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lSQUlqUG9qWVZpMHNVY3gveFRkbkh1Z2d3RFFZSktvWklodmNOQVFFTEJRQXcKRlRFVE1CRUdBMVVFQXhNS2JXbHVhV3QxWW1WRFFUQWVGdzB5TVRBMU1qRXdOak00TkRSYUZ3MHlNakExTWpFdwpOak00TkRSYU1BOHhEVEFMQmdOVkJBTVRCR3BoYm1Vd2dnRWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUJEd0F3CmdnRUtBb0lCQVFDL0gvbTM4NGVZTDI4ZE1FWUNMSjljbmxiV3REVkIvbkkwd1dnc3VkNkhlWU9zOVU2ZnRoODQKeDBPTy9SUXE5Y3hrVHdmeW1qVFMvWnNCRUpYajZiMVU2c0hlQXYrUzJuSElLd04zYy9wTWdPcUhaNEFCR3MwSwpRQkttQXd1akd4V0ZrTm42aDVNQzJGRVhmZ0NpMlVRK0xxQUZLR0xyOUYvNkZUVC9icXo2WWtJdEhIS01YQTZPCk1sZmVkbUU3blVVR2VsTUJlTklOQ3dNcjQzZURwVytFZ1dHWklYUDVxRnJpNGpYVis5ZjcyYVFyb3o4aGs4Qm4KeWdBNm9GUGluT29jNXkyUk9xYmQzcU9aK2twdy9jRmZYZmhMbTZNcWNiVllXRXJjWXBPRFhMSURyZzMyRXoxZwpoL0xrN3liaC8xV2FHRE51T3ZRRWozVUU3R1QxWWZMRkFnTUJBQUdqVmpCVU1BNEdBMVVkRHdFQi93UUVBd0lGCm9EQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBVEFNQmdOVkhSTUJBZjhFQWpBQU1COEdBMVVkSXdRWU1CYUEKRkdLNzVMUWJLUjZPQmRZbzNnNTd3cFB2VlN2a01BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQkpERnh6ZTVOSgp1K2pGMVA0MTdPMnRMSmdLQVRuUXI4SWcwckxVbklzRDI2WDZwUjNIYWhEQmhLWEM5bWhXVkJjTXNOMy8xblhUCjYvcVdBTGRpT2tkYnNWekF5K1NTc253UWc4dnZ1WC85RjM5VUZGYzAyZUZ3Wk96Q1ZaS09LTllia3lEOWN4L1gKd3R3NGVCQnJ3aXpaSFQxSmh0TnZQTEF4bm5LNlVtRjFyaEU2OHlnQTJHWVQ5bHdUNGhRSGJyVElCTGpSVU9WTwp1SE9vYUVPWjZRNE95TGl3YjBlZk5hM082V1VLMi9vNld2MGt1UnRSSTR5VDFFVGN4MXZyRmQ5SlNZTW42MW80CnhYZ0hLRnNPcDdXWmJLVEpWNUt0S3ZkS3Y0K1JUbHVBaXA2OGNudjNXK2hydmtjSzJCOEVtSDFDTldCclYxM1QKRGhGcHdqT0wxeVUxCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  conditions:
    - lastTransitionTime: "2021-05-21T06:43:44Z"
      lastUpdateTime: "2021-05-21T06:43:44Z"
      message: This CSR was approved by kubectl certificate approve.
      reason: KubectlApprove
      status: "True"
      type: Approved
```

### Base64 Decode CSR

<sub>spec.request</sub>

```yaml
...
...
...

spec:
  groups:
    - system:masters
    - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbUZ1WlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUw4ZitiZnpoNWd2Yngwd1JnSXNuMXllVnRhME5VSCtjalRCYUN5NTNvZDVnNnoxClRwKzJIempIUTQ3OUZDcjF6R1JQQi9LYU5OTDltd0VRbGVQcHZWVHF3ZDRDLzVMYWNjZ3JBM2R6K2t5QTZvZG4KZ0FFYXpRcEFFcVlEQzZNYkZZV1EyZnFIa3dMWVVSZCtBS0xaUkQ0dW9BVW9ZdXYwWC9vVk5QOXVyUHBpUWkwYwpjb3hjRG80eVY5NTJZVHVkUlFaNlV3RjQwZzBMQXl2amQ0T2xiNFNCWVpraGMvbW9XdUxpTmRYNzEvdlpwQ3VqClB5R1R3R2ZLQURxZ1UrS2M2aHpuTFpFNnB0M2VvNW42U25EOXdWOWQrRXVib3lweHRWaFlTdHhpazROY3NnT3UKRGZZVFBXQ0g4dVR2SnVIL1Zab1lNMjQ2OUFTUGRRVHNaUFZoOHNVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQ0dWV1ZMbG1EQ3EyMkllUDlUZzJUazdWSCtsNkFzK3pDQUF5c1Y2UWxlaW0waCtTY1Rzdlc2CkNxK1V2ZEJKWVlDZVR1T1pSYm9za0g2VnFEcllDTUVtYUtkRzRCbDlCL1hWR3BzWTlab1c1YzV0aTdFRU0zK2YKaEQxYjZSY2FPUEk4aitmSmZieGlPVTFQMjJtQnR5b1BnVmRScjk3cFN4bmNvMlR2VFYrOUx2QzRmRlB5Yy9ubApQT3A5ZytMVERNSzdiSDlHa3FBU1VLaEIwNlNMNlVsaW9SdTZyNloxOStDQUZwTjFYK0hwVytMOWZLY2F0K0w3CncvQUpGUER2bW14Z3BkVXpWVGdLRG90ZWlHWE9CRm1uYSt2ZVZncjBFU2s2bldGVEgvQ2JHTmtrdWUrNEZCN3YKWWZDZ3pTTXQ1RHU3Y2NnVWhaMCsrbkxFQWNPUjV1bmYKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
  signerName: kubernetes.io/legacy-unknown

...
...
...
```

<sub>Base64 Decode</sub>

```shell
echo "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbUZ1WlRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQUw4ZitiZnpoNWd2Yngwd1JnSXNuMXllVnRhME5VSCtjalRCYUN5NTNvZDVnNnoxClRwKzJIempIUTQ3OUZDcjF6R1JQQi9LYU5OTDltd0VRbGVQcHZWVHF3ZDRDLzVMYWNjZ3JBM2R6K2t5QTZvZG4KZ0FFYXpRcEFFcVlEQzZNYkZZV1EyZnFIa3dMWVVSZCtBS0xaUkQ0dW9BVW9ZdXYwWC9vVk5QOXVyUHBpUWkwYwpjb3hjRG80eVY5NTJZVHVkUlFaNlV3RjQwZzBMQXl2amQ0T2xiNFNCWVpraGMvbW9XdUxpTmRYNzEvdlpwQ3VqClB5R1R3R2ZLQURxZ1UrS2M2aHpuTFpFNnB0M2VvNW42U25EOXdWOWQrRXVib3lweHRWaFlTdHhpazROY3NnT3UKRGZZVFBXQ0g4dVR2SnVIL1Zab1lNMjQ2OUFTUGRRVHNaUFZoOHNVQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRQ0dWV1ZMbG1EQ3EyMkllUDlUZzJUazdWSCtsNkFzK3pDQUF5c1Y2UWxlaW0waCtTY1Rzdlc2CkNxK1V2ZEJKWVlDZVR1T1pSYm9za0g2VnFEcllDTUVtYUtkRzRCbDlCL1hWR3BzWTlab1c1YzV0aTdFRU0zK2YKaEQxYjZSY2FPUEk4aitmSmZieGlPVTFQMjJtQnR5b1BnVmRScjk3cFN4bmNvMlR2VFYrOUx2QzRmRlB5Yy9ubApQT3A5ZytMVERNSzdiSDlHa3FBU1VLaEIwNlNMNlVsaW9SdTZyNloxOStDQUZwTjFYK0hwVytMOWZLY2F0K0w3CncvQUpGUER2bW14Z3BkVXpWVGdLRG90ZWlHWE9CRm1uYSt2ZVZncjBFU2s2bldGVEgvQ2JHTmtrdWUrNEZCN3YKWWZDZ3pTTXQ1RHU3Y2NnVWhaMCsrbkxFQWNPUjV1bmYKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==" | base64 --decode
```

```
-----BEGIN CERTIFICATE-----
MIIDBTCCAe2gAwIBAgIRAIjPojYVi0sUcx/xTdnHuggwDQYJKoZIhvcNAQELBQAw
FTETMBEGA1UEAxMKbWluaWt1YmVDQTAeFw0yMTA1MjEwNjM4NDRaFw0yMjA1MjEw
NjM4NDRaMA8xDTALBgNVBAMTBGphbmUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAw
ggEKAoIBAQC/H/m384eYL28dMEYCLJ9cnlbWtDVB/nI0wWgsud6HeYOs9U6fth84
x0OO/RQq9cxkTwfymjTS/ZsBEJXj6b1U6sHeAv+S2nHIKwN3c/pMgOqHZ4ABGs0K
QBKmAwujGxWFkNn6h5MC2FEXfgCi2UQ+LqAFKGLr9F/6FTT/bqz6YkItHHKMXA6O
MlfedmE7nUUGelMBeNINCwMr43eDpW+EgWGZIXP5qFri4jXV+9f72aQroz8hk8Bn
ygA6oFPinOoc5y2ROqbd3qOZ+kpw/cFfXfhLm6MqcbVYWErcYpODXLIDrg32Ez1g
h/Lk7ybh/1WaGDNuOvQEj3UE7GT1YfLFAgMBAAGjVjBUMA4GA1UdDwEB/wQEAwIF
oDATBgNVHSUEDDAKBggrBgEFBQcDATAMBgNVHRMBAf8EAjAAMB8GA1UdIwQYMBaA
FGK75LQbKR6OBdYo3g57wpPvVSvkMA0GCSqGSIb3DQEBCwUAA4IBAQBJDFxze5NJ
u+jF1P417O2tLJgKATnQr8Ig0rLUnIsD26X6pR3HahDBhKXC9mhWVBcMsN3/1nXT
6/qWALdiOkdbsVzAy+SSsnwQg8vvuX/9F39UFFc02eFwZOzCVZKOKNYbkyD9cx/X
wtw4eBBrwizZHT1JhtNvPLAxnnK6UmF1rhE68ygA2GYT9lwT4hQHbrTIBLjRUOVO
uHOoaEOZ6Q4OyLiwb0efNa3O6WUK2/o6Wv0kuRtRI4yT1ETcx1vrFd9JSYMn61o4
xXgHKFsOp7WZbKTJV5KtKvdKv4+RTluAip68cnv3W+hrvkcK2B8EmH1CNWBrV13T
DhFpwjOL1yU1
-----END CERTIFICATE-----
```

## API Structure in Kubernetes

{{<mermaid>}}

graph TD;

K[kube-api server] --> S[Scheduler]

K --> C[Controller Manager]

C --> CA[CSR-APPROVING]

C --> CS[CSR-SIGNING]

{{</mermaid>}}

### `/etc/kubernetes/manifests/kube-controller-manager.yaml`

{{<admonition note "--cluster-signing-cert-file" true>}}  
Set `ca.crt` file.  
{{</admonition>}}

{{<admonition note "--cluster-signing-key-file" true>}}  
Set `ca.key` file.  
{{</admonition>}}

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-controller-manager
    tier: control-plane
  name: kube-controller-manager
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-controller-manager
        - --allocate-node-cidrs=true
        - --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
        - --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
        - --bind-address=127.0.0.1
        - --client-ca-file=/var/lib/minikube/certs/ca.crt
        - --cluster-cidr=10.244.0.0/16
        - --cluster-name=mk
        - --cluster-signing-cert-file=/var/lib/minikube/certs/ca.crt
        - --cluster-signing-key-file=/var/lib/minikube/certs/ca.key
        - --controllers=*,bootstrapsigner,tokencleaner
        - --kubeconfig=/etc/kubernetes/controller-manager.conf
        - --leader-elect=false
        - --port=0
        - --requestheader-client-ca-file=/var/lib/minikube/certs/front-proxy-ca.crt
        - --root-ca-file=/var/lib/minikube/certs/ca.crt
        - --service-account-private-key-file=/var/lib/minikube/certs/sa.key
        - --service-cluster-ip-range=10.96.0.0/12
        - --use-service-account-credentials=true
      image: k8s.gcr.io/kube-controller-manager:v1.20.2
      imagePullPolicy: IfNotPresent
      livenessProbe:
        failureThreshold: 8
        httpGet:
          host: 127.0.0.1
          path: /healthz
          port: 10257
          scheme: HTTPS
        initialDelaySeconds: 10
        periodSeconds: 10
        timeoutSeconds: 15
      name: kube-controller-manager
      resources:
        requests:
          cpu: 200m
      startupProbe:
        failureThreshold: 24
        httpGet:
          host: 127.0.0.1
          path: /healthz
          port: 10257
          scheme: HTTPS
        initialDelaySeconds: 10
        periodSeconds: 10
        timeoutSeconds: 15
      volumeMounts:
        - mountPath: /etc/ssl/certs
          name: ca-certs
          readOnly: true
        - mountPath: /etc/ca-certificates
          name: etc-ca-certificates
          readOnly: true
        - mountPath: /usr/libexec/kubernetes/kubelet-plugins/volume/exec
          name: flexvolume-dir
        - mountPath: /var/lib/minikube/certs
          name: k8s-certs
          readOnly: true
        - mountPath: /etc/kubernetes/controller-manager.conf
          name: kubeconfig
          readOnly: true
        - mountPath: /usr/local/share/ca-certificates
          name: usr-local-share-ca-certificates
          readOnly: true
        - mountPath: /usr/share/ca-certificates
          name: usr-share-ca-certificates
          readOnly: true
  hostNetwork: true
  priorityClassName: system-node-critical
  volumes:
    - hostPath:
        path: /etc/ssl/certs
        type: DirectoryOrCreate
      name: ca-certs
    - hostPath:
        path: /etc/ca-certificates
        type: DirectoryOrCreate
      name: etc-ca-certificates
    - hostPath:
        path: /usr/libexec/kubernetes/kubelet-plugins/volume/exec
        type: DirectoryOrCreate
      name: flexvolume-dir
    - hostPath:
        path: /var/lib/minikube/certs
        type: DirectoryOrCreate
      name: k8s-certs
    - hostPath:
        path: /etc/kubernetes/controller-manager.conf
        type: FileOrCreate
      name: kubeconfig
    - hostPath:
        path: /usr/local/share/ca-certificates
        type: DirectoryOrCreate
      name: usr-local-share-ca-certificates
    - hostPath:
        path: /usr/share/ca-certificates
        type: DirectoryOrCreate
      name: usr-share-ca-certificates
status: { }
```

## Server Certificates for Servers

### `kube-api.service`

- CA CERT for ETCD
- CERT for ETCD
- KEY for ETCD
- CA CERT for KUBELET
- CERT for KUBELET CLIENT
- KEY for KUBELET CLIENT
- CERT for Service Account
- CERT for TLS
- KEY for TLS

### `kubelet-config.yaml`

- tlsCertFile
- tlsPrivateKeyFile

## Client Certificates for Clients

### `kube-scheduler`

- CA CERT for Cluster Signing
- CA KEY for Cluster Signing
- CA CERT
- KEY for SERVICE ACCOUNT

### `kube-controller-manager`

- CA CERT for Cluster Signing
- CA KEY for Cluster Signing
- CA CERT
- KEY for SERVICE ACCOUNT

### `kubelet`

- CA CERT for Cluster Signing
- CA KEY for Cluster Signing
- CA CERT
- KEY for SERVICE ACCOUNT

### `kube-proxy`

- CA CERT for Cluster Signing
- CA KEY for Cluster Signing
- CA CERT
- KEY for SERVICE ACCOUNT

## Provision Certificate Authority

### CA(Certificate Authority)

- ca-key.pem
- ca.pem

### `kube-apiserver`

- kubernetes-key.pem
- kubernetes.pem

### Admin User

- admin-key.pem
- admin.pem

### `kube-controller-manager`

- kcm-key.pem
- kcm.pem

### `kube-scheduler`

- kube-scheduler-key.pem
- kube-scheduler.pem

### `kube-proxy`

- kube-proxy-key.pem
- kube-proxy.pem

### `kubelet`

- worker01-key.pem
- worker01.pem
- worker02-key.pem
- worker02.pem



