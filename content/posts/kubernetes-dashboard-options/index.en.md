---
title: "Kubernetes Dashboard Options"

date: 2021-07-27T19:43:55+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- dashboard

keywords:

- kubernetes
- dashboard

---

## Change Dashboard Deployment Options

### Backup Deployment

```shell
kubectl -n kubernetes-dashboard get deploy kubernetes-dashboard -o yaml > deploy-kubernetes-dashboard.yaml
```

### Edit Deployment

```shell
kubectl -n kubernetes-dashboard edit deploy kubernetes-dashboard
```

```yaml
  template:
    spec:
      containers:
        - args:
            - --namespace=kubernetes-dashboard
            - --authentication-mode=token      # change or delete, "token" is default
            - --auto-generate-certificates     # add
          #- --enable-skip-login=true          # delete or set to false
          #- --enable-insecure-login           # delete
          image: kubernetesui/dashboard:v2.0.3
          imagePullPolicy: Always
          name: kubernetes-dashboard
```

## Change Dashboard Service

### Backup Service

```shell
kubectl -n kubernetes-dashboard get svc kubernetes-dashboard -o yaml > svc-kubernetes-dashboard.yaml
```

### Edit Service

```shell
kubectl -n kubernetes-dashboard edit svc kubernetes-dashboard
```

```yaml
 spec:
   clusterIP: 10.107.176.19
   externalTrafficPolicy: Cluster
   ports:
     - name: http
       nodePort: 32513  # delete
       port: 9090
       protocol: TCP
       targetPort: 9090
     - name: https
       nodePort: 32441  # delete
       port: 443
       protocol: TCP
       targetPort: 8443
   selector:
     k8s-app: kubernetes-dashboard
   sessionAffinity: None
   type: ClusterIP          # change or delete
 status:
   loadBalancer: { }
```


