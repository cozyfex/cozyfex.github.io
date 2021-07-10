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
| RequestReceived | The stage for events generated as soon as the audit handler receives the request, and before it is delegated down the handler chain. |  
| ResponseStarted | Once the response headers are sent, but before the response body is sent. This stage is only generated for long-running requests (e.g. watch). |  
| ResponseComplete | The response body has been completed and no more bytes will be sent. |  
| Panic | Events generated when a panic occurred. |

## Audit Policy

| Policy | Description |  
|:-:|:-|  
| None | don't log events that match this rule. |  
| Metadata | log request metadata (requesting user, timestamp, resource, verb, etc.) but not request or response body. |  
| Request | log event metadata and request body but not response body. This does not apply for non-resource requests. |  
| RequestResponse | log event metadata, request and response bodies. This does not apply for non-resource requests. |

## Config Audit

{{<admonition note Audit true>}}  
Log file in `--audit-log-path`  
Policy file in `--audit-policy-file`
Log max age in `--audit-log-maxage`

You have to make `volumes` and `volumeMounts` in `kube-apiserver.yaml`
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
