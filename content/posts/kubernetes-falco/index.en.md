---
title: "Kubernetes Falco"

date: 2021-07-09T20:37:03+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- falco

tags:

- kubernetes
- falco
- analytics
- syscalls

keywords:

- kubernetes
- falco
- analytics
- syscalls

---

## Perform Behaviour Analytics of syscalls

{{<admonition note "Analytics of syscalls" true>}}  
We have to be noticed by unexpected executing of syscalls by crackers.  
So we have to build a system for that.  
{{</admonition>}}

## Falco

{{<admonition note Falco true>}}  
[Falco](https://falco.org), the cloud-native runtime security project, is the de facto Kubernetes threat detection engine  
{{</admonition>}}

## Falco Architecture

{{<image src="falco.png" width="100%">}}

## Falco Install as a Package

### Download

```shell
curl -s https://falco.org/repo/falcosecurity-3672BA8F.asc | apt-key add -
echo "deb https://download.falco.org/packages/deb stable main" | tee -a /etc/apt/sources.list.d/falcosecurity.list
apt-get update -y
```

### Install Kernel Headers

```shell
apt-get -y install linux-headers-$(uname -r)
```

### Install Falco

```shell
apt-get install -y falco
```

### Run Falco

```shell
systemctl start falco
```

## Falco Install as a DaemonSet

{{<admonition note HELM true>}}  
[Helm](https://helm.sh), The package manager for Kubernetes  
{{</admonition>}}

### Add Repository

```shell
helm repo add falcosecurity https://falcosecurity.github.io/charts
helm repo update
```

### Install Falco

```shell
helm install falco falcosecurity/falco
```

### Check Running

```shell
kubectl get pods
```

## Check Running Falco Each Node

```shell
systemctl status falco
```

## Analytics Falco

### Create a POD

```shell
kubectl run nginx --image=nginx
```

### Check Running Node

```shell
kubectl get pods -o wide
```

### Connect to the Node

```shell
ssh node01
```

### Trace Falco Running Process

```shell
journalctl -fu falco
```

### Execute Bash on the Node with Extra Terminal

```shell
kubectl exec -it nginx -- bash
```

## Falco Rules

### Structure

```yaml
- rule: <Name of the Rule>
  desc: <Detailed Description of the Rule>
  condition: <When to filter events matching the rule>
  output: <Output to be generated for the Event>
  priority: <Severity of the event>
```

### Sample

```yaml
- rule: Detect Shell inside a container
  desc: Alter if a shell such as bash is open inside the container
  condition: container.id != host and proc.name = bash
  output: Bash Shell Opened (user=%user.name %container.id)
  priority: WARNING
```

### Values in Falco Rules

{{<mermaid>}}

graph TD;

ID(container.id)

N(proc.name)

FN(fd.name)

{{</mermaid>}}

{{<mermaid>}}

graph TD;

ET(evt.type)

UN(user.name)

CR(container.image.repository)

{{</mermaid>}}

### Falco Priority

| Priority |  
|:-:|  
| DEBUG |  
| INFORMATION |  
| NOTICE |  
| WARNING |  
| ERROR |  
| CRITICAL |  
| ALERT |  
| EMERGENCY |

### List in Falco Rules

```yaml
- rule: Detect Shell inside a container
  desc: Alter if a shell such as bash is open inside the container
  condition: container.id != host and proc.name in (linux_shells)
  output: Bash Shell Opened (user=%user.name %container.id)
  priority: WARNING
- list: linux_shells
  items: [ bash, zsh, ksh, sh, csh ]
```

{{<admonition note List true>}}  
You can see `linux_shells`
{{</admonition>}}

### Macro in Falco Rules

```yaml
- rule: Detect Shell inside a container
  desc: Alter if a shell such as bash is open inside the container
  condition: container and proc.name in (linux_shells)
  output: Bash Shell Opened (user=%user.name %container.id)
  priority: WARNING
- list: linux_shells
  items: [ bash, zsh, ksh, sh, csh ]
- macro: container
  condition: container.id != host
```

{{<admonition note Macro true>}}  
You can see `container`
{{</admonition>}}

## Falco Configuration Files

### Falco Main Config File

```shell
cat /etc/falco/falco.yaml
```

### Figure Out the Config on Running Process

#### Check Running Log

```shell
journalctl -fu falco
```

```
-- Logs begin at Tue 2021-04-13 21:45:35 UTC, end at Tue 2021-04-13 21:51:31 UTC. --
Apr 13 21:45:36 node01 systemd[1]: Starting Falco: Container Native Runtime Security...
Apr 13 21:45:36 node01 systemd[1]: Started Falco: Container Native Runtime Security.
Apr 13 21:45:36 node01 falco[9817]: Falco version 0.28.0 (driver version 5c0b863ddade7a45568c0ac97d037422c9efb750)
Apr 13 21:45:36 node01 falco[9817]: Tue Apr 13 21:45:36 2021: Falco version 0.28.0 (driver version 5c0b863ddade7a45568c0ac97d037422c9efb750)
Apr 13 21:45:36 node01 falco[9817]: Falco initialized with configuration file /etc/falco/falco.yaml
Apr 13 21:45:36 node01 falco[9817]: Tue Apr 13 21:45:36 2021: Falco initialized with configuration file /etc/falco/falco.yaml
```

#### Check Service Config

```shell
cat /usr/lib/systemd/system/falco.service
```

```
[Unit]
Description=Falco: Container Native Runtime Security
Documentation=https://falco.org/docs/

[Service]
Type=simple
User=root
ExecStartPre=/sbin/modprobe falco
ExecStart=/usr/bin/falco --pidfile=/var/run/falco.pid -c /etc/falco/falco.yaml
.
.
.
```

### `/etc/falco/falco/yaml`

```yaml
#
# Copyright (C) 2021 The Falco Authors.
#
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# File(s) or Directories containing Falco rules, loaded at startup.
# The name "rules_file" is only for backwards compatibility.
# If the entry is a file, it will be read directly. If the entry is a directory,
# every file in that directory will be read, in alphabetical order.
#
# falco_rules.yaml ships with the falco package and is overridden with
# every new software version. falco_rules.local.yaml is only created
# if it doesn't exist. If you want to customize the set of rules, add
# your customizations to falco_rules.local.yaml.
#
# The files will be read in the order presented here, so make sure if
# you have overrides they appear in later files.
rules_file:
  - /etc/falco/falco_rules.yaml
  - /etc/falco/falco_rules.local.yaml
  - /etc/falco/k8s_audit_rules.yaml
  - /etc/falco/rules.d

# If true, the times displayed in log messages and output messages
# will be in ISO 8601. By default, times are displayed in the local
# time zone, as governed by /etc/localtime.
time_format_iso_8601: false

# Whether to output events in json or text
json_output: false

# When using json output, whether or not to include the "output" property
# itself (e.g. "File below a known binary directory opened for writing
# (user=root ....") in the json output.
json_include_output_property: true

# Send information logs to stderr and/or syslog Note these are *not* security
# notification logs! These are just Falco lifecycle (and possibly error) logs.
log_stderr: true
log_syslog: true

# Minimum log level to include in logs. Note: these levels are
# separate from the priority field of rules. This refers only to the
# log level of falco's internal logging. Can be one of "emergency",
# "alert", "critical", "error", "warning", "notice", "info", "debug".
log_level: info

# Minimum rule priority level to load and run. All rules having a
# priority more severe than this level will be loaded/run.  Can be one
# of "emergency", "alert", "critical", "error", "warning", "notice",
# "info", "debug".
priority: debug

# Whether or not output to any of the output channels below is
# buffered. Defaults to false
buffered_outputs: false

# Falco uses a shared buffer between the kernel and userspace to pass
# system call information. When Falco detects that this buffer is
# full and system calls have been dropped, it can take one or more of
# the following actions:
#   - ignore: do nothing (default when list of actions is empty)
#   - log: log a DEBUG message noting that the buffer was full
#   - alert: emit a Falco alert noting that the buffer was full
#   - exit: exit Falco with a non-zero rc
#
# Notice it is not possible to ignore and log/alert messages at the same time.
#
# The rate at which log/alert messages are emitted is governed by a
# token bucket. The rate corresponds to one message every 30 seconds
# with a burst of one message (by default).
#
# The messages are emitted when the percentage of dropped system calls
# with respect the number of events in the last second
# is greater than the given threshold (a double in the range [0, 1]).
#
# For debugging/testing it is possible to simulate the drops using
# the `simulate_drops: true`. In this case the threshold does not apply.

syscall_event_drops:
  threshold: .1
  actions:
    - log
    - alert
  rate: .03333
  max_burst: 1

# Falco uses a shared buffer between the kernel and userspace to receive
# the events (eg., system call information) in userspace.
#
# Anyways, the underlying libraries can also timeout for various reasons.
# For example, there could have been issues while reading an event.
# Or the particular event needs to be skipped.
# Normally, it's very unlikely that Falco does not receive events consecutively.
#
# Falco is able to detect such uncommon situation.
#
# Here you can configure the maximum number of consecutive timeouts without an event
# after which you want Falco to alert.
# By default this value is set to 1000 consecutive timeouts without an event at all.
# How this value maps to a time interval depends on the CPU frequency.

syscall_event_timeouts:
  max_consecutives: 1000

# Falco continuously monitors outputs performance. When an output channel does not allow
# to deliver an alert within a given deadline, an error is reported indicating
# which output is blocking notifications.
# The timeout error will be reported to the log according to the above log_* settings.
# Note that the notification will not be discarded from the output queue; thus,
# output channels may indefinitely remain blocked.
# An output timeout error indeed indicate a misconfiguration issue or I/O problems
# that cannot be recovered by Falco and should be fixed by the user.
#
# The "output_timeout" value specifies the duration in milliseconds to wait before
# considering the deadline exceed.
#
# With a 2000ms default, the notification consumer can block the Falco output
# for up to 2 seconds without reaching the timeout.

output_timeout: 2000

# A throttling mechanism implemented as a token bucket limits the
# rate of falco notifications. This throttling is controlled by the following configuration
# options:
#  - rate: the number of tokens (i.e. right to send a notification)
#    gained per second. Defaults to 1.
#  - max_burst: the maximum number of tokens outstanding. Defaults to 1000.
#
# With these defaults, falco could send up to 1000 notifications after
# an initial quiet period, and then up to 1 notification per second
# afterward. It would gain the full burst back after 1000 seconds of
# no activity.

outputs:
  rate: 1
  max_burst: 1000

# Where security notifications should go.
# Multiple outputs can be enabled.

syslog_output:
  enabled: true

# If keep_alive is set to true, the file will be opened once and
# continuously written to, with each output message on its own
# line. If keep_alive is set to false, the file will be re-opened
# for each output message.
#
# Also, the file will be closed and reopened if falco is signaled with
# SIGUSR1.

file_output:
  enabled: false
  keep_alive: false
  filename: ./events.txt

stdout_output:
  enabled: true

# Falco contains an embedded webserver that can be used to accept K8s
# Audit Events. These config options control the behavior of that
# webserver. (By default, the webserver is enabled).
#
# The ssl_certificate is a combination SSL Certificate and corresponding
# key contained in a single file. You can generate a key/cert as follows:
#
# $ openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem
# $ cat certificate.pem key.pem > falco.pem
# $ sudo cp falco.pem /etc/falco/falco.pem
#
# It also exposes a healthy endpoint that can be used to check if Falco is up and running
# By default the endpoint is /healthz
webserver:
  enabled: true
  listen_port: 8765
  k8s_audit_endpoint: /k8s-audit
  k8s_healthz_endpoint: /healthz
  ssl_enabled: false
  ssl_certificate: /etc/falco/falco.pem

# Possible additional things you might want to do with program output:
#   - send to a slack webhook:
#         program: "jq '{text: .output}' | curl -d @- -X POST https://hooks.slack.com/services/XXX"
#   - logging (alternate method than syslog):
#         program: logger -t falco-test
#   - send over a network connection:
#         program: nc host.example.com 80

# If keep_alive is set to true, the program will be started once and
# continuously written to, with each output message on its own
# line. If keep_alive is set to false, the program will be re-spawned
# for each output message.
#
# Also, the program will be closed and reopened if falco is signaled with
# SIGUSR1.
program_output:
  enabled: false
  keep_alive: false
  program: "jq '{text: .output}' | curl -d @- -X POST https://hooks.slack.com/services/XXX"

http_output:
  enabled: false
  url: http://some.url

# Falco supports running a gRPC server with two main binding types
# 1. Over the network with mandatory mutual TLS authentication (mTLS)
# 2. Over a local unix socket with no authentication
# By default, the gRPC server is disabled, with no enabled services (see grpc_output)
# please comment/uncomment and change accordingly the options below to configure it.
# Important note: if Falco has any troubles creating the gRPC server
# this information will be logged, however the main Falco daemon will not be stopped.
# gRPC server over network with (mandatory) mutual TLS configuration.
# This gRPC server is secure by default so you need to generate certificates and update their paths here.
# By default the gRPC server is off.
# You can configure the address to bind and expose it.
# By modifying the threadiness configuration you can fine-tune the number of threads (and context) it will use.
# grpc:
#   enabled: true
#   bind_address: "0.0.0.0:5060"
#   # when threadiness is 0, Falco sets it by automatically figuring out the number of online cores
#   threadiness: 0
#   private_key: "/etc/falco/certs/server.key"
#   cert_chain: "/etc/falco/certs/server.crt"
#   root_certs: "/etc/falco/certs/ca.crt"

# gRPC server using an unix socket
grpc:
  enabled: false
  bind_address: "unix:///var/run/falco.sock"
  # when threadiness is 0, Falco automatically guesses it depending on the number of online cores
  threadiness: 0

# gRPC output service.
# By default it is off.
# By enabling this all the output events will be kept in memory until you read them with a gRPC client.
# Make sure to have a consumer for them or leave this disabled.
grpc_output:
  enabled: false
```

### `/etc/falco/falco_rules.yaml`

```yaml
- rule: Terminal shell in container
  desc: A shell was used as the entrypoint/exec point into a container with an attached terminal.
  condition: >
    spawned_process and container
    and shell_procs and proc.tty != 0
    and container_entrypoint
    and not user_expected_terminal_shell_in_container_conditions
  output: >
    A shell was spawned in a container with an attached terminal (user=%user.name user_loginuid=%user.loginuid %container.info
    shell=%proc.name parent=%proc.pname cmdline=%proc.cmdline terminal=%proc.tty container_id=%container.id image=%container.image.repository)
  priority: NOTICE
  tags: [ container, shell, mitre_execution ]
```

### `/etc/falco/falco_rules.local.yaml`

```yaml
- rule: Terminal shell in container
  desc: A shell was used as the entrypoint/exec point into a container with an attached terminal.
  condition: >
    spawned_process and container
    and shell_procs and proc.tty != 0
    and container_entrypoint
    and not user_expected_terminal_shell_in_container_conditions
  output: >
    A shell was spawned in a container with an attached terminal (user=%user.name user_loginuid=%user.loginuid %container.info
    shell=%proc.name parent=%proc.pname cmdline=%proc.cmdline terminal=%proc.tty container_id=%container.id image=%container.image.repository)
  priority: WNAORTNICNEG

- rule: Anomalous read in kodekloud/webapp pod
  desc: Detect Suspicious reads in custom webapp container
  condition: >
    open_read and container
    and container.image.repository="kodekloud/simple-webapp"
    and fd.directory != "/opt/app"
  output: >
    A file was opened and read outside the /opt/app directory(user=%user.name user_loginuid=%user.loginuid
    container_id=%container.id image=%container.image.repository)
  priority: CRITICAL
```

## Hot Reload

```shell
cat /var/run/falco.pid
```

```shell
kill -1 $(cat /var/run/falco.pid)
```

