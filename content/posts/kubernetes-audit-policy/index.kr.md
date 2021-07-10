---
title: "Kubernetes Audit Policy"

date: 2021-07-10T18:04:28+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- audit

keywords:

- kubernetes
- audit

---

## Audit Stages

| Stage | Description |  
|:-:|:-|  
| RequestReceived | 감사 핸들러가 요청을 받는 즉시 생성된 이벤트들과, 다운체인 핸들러에 위임되기 전의 이벤트입니다. |  
| ResponseStarted | 응답 해더는 전송 되고, 응답 바디는 전송되기 이전의 단계입니다. 이 단계는 장기 실행 요청에서만 생성됩니다.(예: watch) |  
| ResponseComplete | 응답 바디가 완료되고 더 이상 전송이 없는 단계 |  
| Panic | 패닉이 발생되었을 때 생성되는 이벤트 |

## Audit Policy

| Policy | Description |  
|:-:|:-|  
| None | 이 룰에 매칭되는 이벤트들은 기록하지 않는다. |  
| Metadata | 메타데이터를 기록한다 (요청한 user, timestamp, resource, verb, etc.) 그러나 요청이나 응답 바디를 기록하진 않는다. |  
| Request | 이벤트 메타데이터와 요청 바디 이벤트를 기록히지만 응답 바디는 기록하지 않는다. 리소스가 아닌 요청에는 적용되지 않는다. |  
| RequestResponse | 메타데이터, 요청과 응답 바디들의 이벤트를 기록한다. 리소스가 아닌 요청에는 적용되지 않는다. |

## Config Audit

{{<admonition note Audit true>}}  
로그 파일 설정 `--audit-log-path`  
정책 파일 설정 `--audit-policy-file`
로그 최대 생명 주기 `--audit-log-maxage`

`kube-apiserver.yaml` 에 `volumes` 와 `volumeMounts` 를 설정해야 한다.  
{{</admonition>}}

### `/etc/kubernetes/manifests/kube-apiserver.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-apiserver
        - --authorization-mode=Node,RBAC
        - --advertise-address=172.17.0.107 - --allow-privileged=true
        - --enable-bootstrap-token-auth=true
        - --audit-log-path=/var/log/k8-audit.log
        - --audit-policy-file=/etc/kubernetes/audit-policy.yaml
        - --audit-log-maxage=30
```

### `kube-apiserver.service`

```
ExecStart=/usr/local/bin/kube-apiserver \\
    --advertise-address=${INTERNAL_IP} \\
    --allow-privileged=true \\
    --apiserver-count=3 \\
    --authorization-mode=Node,RBAC \\
    --bind-address=0.0.0.0 \\
    --enable-swagger-ui=true \\
    --etcd-servers=https://127.0.0.1:2379 \\
    --event-ttl=1h \\
    --runtime-config=api/all \\
    --service-cluster-ip-range=10.32.0.0/24 \\
    --service-node-port-range=30000-32767 \\
    --v=2
    --audit-log-path=/var/log/k8-audit.log
    --audit-policy-file=/etc/kubernetes/audit-policy.yaml
    --audit-log-maxage=30
```

## Audit Policy Sample

```yaml
apiVersion: audit.k8s.io/v1 # This is required.
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # Log pod changes at RequestResponse level
  - level: RequestResponse
    resources:
      - group: ""
        # Resource "pods" doesn't match requests to any subresource of pods,
        # which is consistent with the RBAC policy.
        resources: [ "pods" ]
  # Log "pods/log", "pods/status" at Metadata level
  - level: Metadata
    resources:
      - group: ""
        resources: [ "pods/log", "pods/status" ]

  # Don't log requests to a configmap called "controller-leader"
  - level: None
    resources:
      - group: ""
        resources: [ "configmaps" ]
        resourceNames: [ "controller-leader" ]

  # Don't log watch requests by the "system:kube-proxy" on endpoints or services
  - level: None
    users: [ "system:kube-proxy" ]
    verbs: [ "watch" ]
    resources:
      - group: "" # core API group
        resources: [ "endpoints", "services" ]

  # Don't log authenticated requests to certain non-resource URL paths.
  - level: None
    userGroups: [ "system:authenticated" ]
    nonResourceURLs:
      - "/api*" # Wildcard matching.
      - "/version"

  # Log the request body of configmap changes in kube-system.
  - level: Request
    resources:
      - group: "" # core API group
        resources: [ "configmaps" ]
    # This rule only applies to resources in the "kube-system" namespace.
    # The empty string "" can be used to select non-namespaced resources.
    namespaces: [ "kube-system" ]

  # Log configmap and secret changes in all other namespaces at the Metadata level.
  - level: Metadata
    resources:
      - group: "" # core API group
        resources: [ "secrets", "configmaps" ]

  # Log all other resources in core and extensions at the Request level.
  - level: Request
    resources:
      - group: "" # core API group
      - group: "extensions" # Version of group should NOT be included.

  # A catch-all rule to log all other requests at the Metadata level.
  - level: Metadata
    # Long-running requests like watches that fall under this rule will not
    # generate an audit event in RequestReceived.
    omitStages:
      - "RequestReceived"
```
