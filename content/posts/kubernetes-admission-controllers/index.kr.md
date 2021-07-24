---
title: "Kubernetes Admission Controllers"

date: 2021-07-06T17:48:24+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- linux

tags:

- kubernetes
- admission controllers
- linux
- capabilities

keywords:

- kubernetes
- admission controllers
- linux
- capabilities

---

## Kubernetes API Process

{{<mermaid>}}

graph LR;

K(kubectl) --> A(Authentication)

A --> AZ(Authorization)

AZ --> AC([Admission Controllers])

AC --> C[Create POD]

{{</mermaid>}}

## Admission Controllers

| Controller |  
|:-:|  
| AlwaysPullImages |  
| DefaultStorageClass |  
| EventRateLimit |  
| NamespaceExists |  
| NamespaceAutoProvision |  
| Many more... |

## Admission Controllers 활성화 확인

```shell
kubectl -n kube-system exec kube-apiserver-controlplane -- kube-apiserver -h | grep enable-admission-plugins
```

## Admission Controllers 설정

### `/etc/kubernetes/manifests/kube-apiserver.yaml`

{{<admonition note Config true>}}  
`--enable-admission-plugins=NodeRestriction`
`--disable-admission-plugins=DefaultStorageClass`
{{</admonition>}}

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 10.11.34.9:6443
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-apiserver
        - --advertise-address=10.11.34.9
        - --allow-privileged=true
        - --authorization-mode=Node,RBAC
        - --client-ca-file=/etc/kubernetes/pki/ca.crt
        - --enable-admission-plugins=NodeRestriction
        - --disable-admission-plugins=DefaultStorageClass
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
      image: k8s.gcr.io/kube-apiserver:v1.20.0
      imagePullPolicy: IfNotPresent
      livenessProbe:
        failureThreshold: 8
        httpGet:
          host: 10.11.34.9
          path: /livez
          port: 6443
          scheme: HTTPS
        initialDelaySeconds: 10
        periodSeconds: 10
        timeoutSeconds: 15
      name: kube-apiserver
      readinessProbe:
        failureThreshold: 3
        httpGet:
          host: 10.11.34.9
          path: /readyz
          port: 6443
          scheme: HTTPS
        periodSeconds: 1
        timeoutSeconds: 15
      resources:
        requests:
          cpu: 250m
      startupProbe:
        failureThreshold: 24
        httpGet:
          host: 10.11.34.9
          path: /livez
          port: 6443
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
        - mountPath: /etc/kubernetes/pki
          name: k8s-certs
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
        path: /etc/kubernetes/pki
        type: DirectoryOrCreate
      name: k8s-certs
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

## Mutating

{{<admonition note Mutating true>}}  
Mutating 은 Validating 이전에 실행된다.  
`NamespaceAutoProvision` 는 뮤테이팅 컨틀롤러 중 하나이다.  
{{</admonition>}}

## Validating

{{<admonition note Validating true>}}  
Validating 은 Mutating 이후에 실행된다.  
`NamespaceExists` 는 벨리데이팅 컨트롤러 중 하나이다.  
{{</admonition>}}

## Webhook

### Namespace 생성

```shell
kubectl create ns webhook-demo
```

### TLS Secret 생성

```shell
kubectl create secret tls webhook-server-tls --cert=/root/keys/webhook-server-tls.crt --key=/root/keys/webhook-server-tls.key --namespace=webhook-demo
```

### Deployment 생성

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webhook-server
  namespace: webhook-demo
  labels:
    app: webhook-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webhook-server
  template:
    metadata:
      labels:
        app: webhook-server
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1234
      containers:
        - name: server
          image: stackrox/admission-controller-webhook-demo:latest
          imagePullPolicy: Always
          ports:
            - containerPort: 8443
              name: webhook-api
          volumeMounts:
            - name: webhook-tls-certs
              mountPath: /run/secrets/tls
              readOnly: true
      volumes:
        - name: webhook-tls-certs
          secret:
            secretName: webhook-server-tls
```

```shell
kubectl create -f webhook-deployment.yaml
```

### Service 생성

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webhook-server
  namespace: webhook-demo
spec:
  selector:
    app: webhook-server
  ports:
    - port: 443
      targetPort: webhook-api
```

```shell
kubectl create -f webhook-service.yaml
```

### MutatingWebhookConfiguration 생성

```yaml
apiVersion: admissionregistration.k8s.io/v1beta1
kind: MutatingWebhookConfiguration
metadata:
  name: demo-webhook
webhooks:
  - name: webhook-server.webhook-demo.svc
    clientConfig:
      service:
        name: webhook-server
        namespace: webhook-demo
        path: "/mutate"
      caBundle: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURQekNDQWllZ0F3SUJBZ0lVSzE1TjhaVFY4YjFVVGptNDNJNGJUSXJDMFdvd0RRWUpLb1pJaHZjTkFRRUwKQlFBd0x6RXRNQ3NHQTFVRUF3d2tRV1J0YVhOemFXOXVJRU52Ym5SeWIyeHNaWElnVjJWaWFHOXZheUJFWlcxdgpJRU5CTUI0WERUSXhNRGN3TmpFd01UZ3pNMW9YRFRJeE1EZ3dOVEV3TVRnek0xb3dMekV0TUNzR0ExVUVBd3drClFXUnRhWE56YVc5dUlFTnZiblJ5YjJ4c1pYSWdWMlZpYUc5dmF5QkVaVzF2SUVOQk1JSUJJakFOQmdrcWhraUcKOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDQVFFQXlkU3VWVHVQaG54MTJIcENSUWxUbXl3WUw4YklGWHRpWExOWgpuVmF4aGcyS2YzWlBabGk2M3ZXQnNHQ3BMc0RaODRuWGpxY0JIaHE5QnVlOXVHdmp0anQ1ckxaUmt3cHVJL2p6CnkxdDNzWmRaZWVGT3g5ZzhBUHlMMHgrYVlBTEZkNEZYbk5TVnJtNGlZMlc3TUxQQ1IzcHErbUJRQk01Tm5aWkMKM3dVZWZjeDMxcm9GWkZCbXNXY0NqVFBRUzVXdVhwbyt2U21Pb3hnM1BaSjJ1RWdTR3REYnhieVIyWU1vUlhpTQp2S01ZSFBNVENOQnRUTVE0L1luWnJkTjhZczQwMFMwT1hPTjg5RGZnR0h5QXhQZkJHY1hmK05XNTNvRW5iUm9BCmVVbjFLQzloeXFxSlc2MWVGbXVZbnVieTlkVHVOcUNIdVJWVld0SGQ4Nk1IazV5RkVRSURBUUFCbzFNd1VUQWQKQmdOVkhRNEVGZ1FVR3RtZVE0S3VlK1V4blY2Mi8yaW5hZk5IWGFBd0h3WURWUjBqQkJnd0ZvQVVHdG1lUTRLdQplK1V4blY2Mi8yaW5hZk5IWGFBd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBTkJna3Foa2lHOXcwQkFRc0ZBQU9DCkFRRUFmQTdiNElndVYwekZ1Y2RWMVllTEtLaGtvR3dwNlJUZzFJektvdW95SE96WS9vVUxJcWVlaTA5NjM4YXUKejhFVWhvYmNhdGZVL1dBc25rUXQ3R0xydUk1a1grWkxmQWl1NmRQZytVUld0Q2NSZXVUQmhKMG9EOVJVeW8xcApQTGJyeUx6KzBlaWFOcHY1L2lGTWZ2TnRTaXl0a1IzQjdIQXlNTkw0RHh1bWNZaEQzUUE1RG5KUDlHUDJQSjNiCjYwUS8rbXIyZHJnQkphTXdpMHpDd0hqKzBLbnh2SUE0UERGS0ZxV0IrYjhtam0xZjJjMjdMODdYT0dGWnQ4bkUKdWo0bGN1cHBqN3p1cytkeGFKQWZ0THpIR0lTRTJPN1JzRUxNT0J0c3J0NzVaWkdubEFxK2Uxb2czTlZtSUF4dQpkTm83MUhnMGprbi9GS0ozaVlKYWMva0Jtdz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    rules:
      - operations: [ "CREATE" ]
        apiGroups: [ "" ]
        apiVersions: [ "v1" ]
        resources: [ "pods" ]
```

```shell
kubectl create -f webhook-configuration.yaml
```

## Examples

### NamespaceExists

{{<admonition note NamespaceExists true>}}  
`blue` 네임스페이스가 존재하지 않으면, 에러를 리턴한다.  
{{</admonition>}}

```shell
kubectl run nginx --image=nginx --namespace=blue
```

```
Error from server (NotFound): namespaces "blue" not found
```

### NamespaceAutoProvision

{{<admonition note NamespaceAutoProvision true>}}  
`blue` 네임스페이스가 존재하지 않으면, `NamespaceAutoProvision`에 의해 해당 네임스페이스가 생성된다.  
{{</admonition>}}

```shell
kubectl run nginx --image=nginx --namespace=blue
```

### DefaultStorageClass

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

```shell
kubectl describe pvc my-pvc | grep -i storageclass
```

```
StorageClass: default
```

### PodSecurityPolicy

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: example-psp
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
    - configMap
    - secret
    - emptyDir
    - hostPath
```


