---
title: "Kubernetes AppArmor"

date: 2021-07-06T16:32:43+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- apparmor

keywords:

- kubernetes
- apparmor

---

## Kubernetes 에서 AppArmor

* Kubernetes Version > 1.4
* AppArmor Kernel Module Enabled
* AppArmor Profile Loaded in the Kernel
* Container Runtime should be Supported

## 정의

{{<admonition note AppArmor true>}}  
container.apparmor.security.beta.kubernetes.io/`<container-name>`: localhost/`<profile-name>`
{{</admonition>}}

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
  annotations:
    container.apparmor.security.beta.kubernetes.io/nginx: localhost/custom-nginx
spec:
  containers:
    - image: 'nginx:alpine'
      name: nginx
      volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: test-volume
  volumes:
    - name: test-volume
      hostPath:
        path: /data/pages
        type: Directory
```

