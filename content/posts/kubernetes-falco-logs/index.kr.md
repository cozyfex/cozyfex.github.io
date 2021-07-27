---
title: "Kubernetes Falco Logs"

date: 2021-07-27T19:24:46+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- falco

tags:

- kubernetes
- falco
- logs

keywords:

- kubernetes
- falco
- logs

---

## Falco Log 설정

### `/etc/falco/falco.yaml`

```yaml
...
# Where security notifications should go.
# Multiple outputs can be enabled.

syslog_output:
  enabled: true
...
```

## System Logs 확인

```shell
cat /var/log/syslog | grep falco
```

