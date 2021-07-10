---
title: "Linux Tracee"

date: 2021-07-05T20:30:59+09:00

author: CozyFex

categories:

- linux
- tracee

tags:

- linux
- kubernetes
- tracee

keywords:

- linux
- kubernetes
- tracee

---

## Tracee

{{<admonition note Tracee true>}}  
Runtime Security and Forensics using [eBPF](https://ebpf.io).  
{{</admonition>}}

## Command Options

### Binding

| Mount | Mode | Description |  
|:-|:-:|:-|  
| /lib/modules/ | Read Only | Kernel Headers |  
| /usr/src | Read Only | Kernel Headers |  
| /tmp/tracee | Default | Default workspace |

### Additional Capability

{{<admonition note Capabiltity true>}}  
`--privileged`
{{</admonition>}}

## Command Examples

### Standard Output(Default)

```shell
docker run --name tracee --rm --privileged -v /lib/modules/:/lib/modules/:ro -v /usr/src:/usr/src:ro -v /tmp/tracee:/tmp/tracee -it aquasec/tracee:latest
```

