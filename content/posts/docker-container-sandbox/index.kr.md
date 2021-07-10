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
일반적으로, 도커같은 평범한 컨테이너 모델은 호스트커널과 공유되는 문제가 있다.  
크래커는 특권 상승을 통해 호스트 시스템을 장악할 수 있다.  
특권 상승은 SELinux 또는 컨테이너 정책에 의해 예방 할순 있지만, 완벽하진 않다.  
그래서 우리는 컨테이너를 박싱할 필요가 있다.  
{{</admonition>}}

## gVisor

{{<admonition note gVisor true>}}  
[gVisor](https://github.com/google/gvisor) 리눅스 시스템 표면의 실질적인 부분을 구현하는 Go로 작성된 어플리케이션 커널입니다.  
이것은 어플리케이션과 호스트 커널간의 격리 경계를 제공하는 runsc라 불리는 OCI(Open Container Initiative)를 포함한다.  
이 runsc 런타임은 도커 및 쿠버네티스와 통합되어 센드박스 컨테이너 실행을 간단하게 만든다.  
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
[Kata Containers](https://katacontainers.io)는 오픈소스 컨테이너 런타임으로, 컨테이너 생태에 원활하게 연결되는 경량 가상머신을 구축한다.  
{{</admonition>}}

{{<mermaid>}}

graph TD;

CA[Container A<br><br>CPU & RAM<br>VM Kernel] --> D(Docker)

CB[Container B<br><br>CPU & RAM<br>VM Kernel] --> D

CC[Container C<br><br>CPU & RAM<br>VM Kernel] --> D

D --> H(Host OS)

{{</mermaid>}}
