---
title: "Docker Container Sandbox"

date: 2021-07-07T21:27:53+09:00

author: CozyFex

categories:

- docker
- sandbox
- gvisor
- kata containers

tags:

- docker
- sandbox
- gvisor
- kata containers

keywords:

- docker
- sandbox
- gvisor
- kata containers

---

## Docker Vulnerable

{{<admonition note "Docker Vulnerable" true>}}  
Typically, Docker that is like normally container model has a problem that the container is sharing with host kernel.  
The cracker can conquer the host system by privilege escalating.  
The privilege escalating is prevented by SELinux or container policies, but it's not perfect.  
So we need to boxing the containers.  
{{</admonition>}}

## gVisor

{{<admonition note gVisor true>}}  
[gVisor](https://github.com/google/gvisor) is an application kernel, written in Go, that implements a substantial portion of the Linux system surface.  
It includes an Open Container Initiative (OCI) runtime called runsc that provides an isolation boundary between the application and the host kernel.  
The runsc runtime integrates with Docker and Kubernetes, making it simple to run sandboxed containers.  
{{</admonition>}}

{{<mermaid>}}

graph TD;

CA[Container A] --> SA(syscall)

CB[Container B] --> SB(syscall)

CC[Container C] --> SC(syscall)

SA --> GA((gVisor))

SB --> GB((gVisor))

SC --> GC((gVisor))

GA --> L[Host Linux Kernel]

GB --> L

GC --> L

{{</mermaid>}}

## Kata Containers

{{<admonition note "Kata Containers" true>}}  
[Kata Containers](https://katacontainers.io) is an open source container runtime, building lightweight virtual machines that seamlessly plug into the containers ecosystem.  
{{</admonition>}}

{{<mermaid>}}

graph TD;

CA[Container A<br><br>CPU & RAM<br>VM Kernel] --> D(Docker)

CB[Container B<br><br>CPU & RAM<br>VM Kernel] --> D

CC[Container C<br><br>CPU & RAM<br>VM Kernel] --> D

D --> H(Host OS)

{{</mermaid>}}
