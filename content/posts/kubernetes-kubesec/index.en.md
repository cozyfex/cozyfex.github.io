---
title: "Kubernetes KUBESEC"

date: 2021-07-08T23:28:16+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- kubesec

tags:

- kubernetes
- kubesec

keywords:

- kubernetes
- kubesec

---

## KUBESEC

{{<admonition note KUBESEC true>}}  
Security risk analysis for Kubernetes resources  
[KUBESEC.IO](https://kubesec.io)
{{</admonition>}}

## `kubesec-test.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubesec-demo
spec:
  containers:
    - name: kubesec-demo
      image: gcr.io/google-samples/node-hello:1.0
      securityContext:
        readOnlyRootFilesystem: true
```

## Command Line Usage

```shell
kubesec scan kubesec-test.yaml
```

## Docker Usage

```shell
docker run -i kubesec/kubesec:512c5e0 scan /dev/stdin < kubesec-test.yaml
```

## HTTP Server

### CLI with HTTP

#### Run Background

```shell
kubesec http 8080 &
```

```
[1] 12345
{"severity":"info","timestamp":"2019-05-12T11:58:34.662+0100","caller":"server/server.go:69","message":"Starting HTTP server on port 8080"}
```

#### Test

```shell
curl -sSX POST --data-binary @test/asset/score-0-cap-sys-admin.yml http://localhost:8080/scan
```

```
[
  {
    "object": "Pod/security-context-demo.default",
    "valid": true,
    "message": "Failed with a score of -30 points",
    "score": -30,
    "scoring": {
      "critical": [
        {
          "selector": "containers[] .securityContext .capabilities .add == SYS_ADMIN",
          "reason": "CAP_SYS_ADMIN is the most privileged capability and should always be avoided"
        },
        {
          "selector": "containers[] .securityContext .runAsNonRoot == true",
          "reason": "Force the running image to run as a non-root user to ensure least privilege"
        },
  // ...
```

#### Kill the Process

```shell
kill %
```

### Docker with HTTP

#### Run Docker

```shell
docker run -d -p 8080:8080 kubesec/kubesec:512c5e0 http 8080
```

#### Test

```shell
curl -sSX POST --data-binary @test/asset/score-0-cap-sys-admin.yml http://localhost:8080/scan
```

## Kubesec-as-a-Service

```shell
curl -sSX POST --data-binary @"k8s-deployment.yaml" https://v2.kubesec.io/scan
```


