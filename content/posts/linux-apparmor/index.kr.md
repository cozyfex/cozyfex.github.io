---
title: "Linux AppArmor"

date: 2021-07-06T15:18:53+09:00

author: CozyFex

categories:

- linux
- apparmor

tags:

- linux
- kubernetes
- apparmor

keywords:

- linux
- kubernetes
- apparmor

---

## AppArmor

{{<admonition note AppArmor true>}}  
리눅스 커널 보안 모듈  
{{</admonition>}}

## Status

```shell
systemctl status apparmor
```

```
● apparmor.service - AppArmor initialization
   Loaded: loaded (/lib/systemd/system/apparmor.service; enabled; vendor preset: enabled)
   Active: active (exited) since Tue 2021-07-06 06:31:44 UTC; 6min ago
     Docs: man:apparmor(7)
           http://wiki.apparmor.net/
 Main PID: 317 (code=exited, status=0/SUCCESS)
    Tasks: 0 (limit: 4678)
   CGroup: /system.slice/apparmor.service
```

## 활성화 확인

```shell
cat /sys/module/apparmor/parameters/enabled
```

```
Y
```

## Profiles 확인

```shell
cat /sys/kernel/security/apparmor/profiles
```

```
/usr/{sbin/traceroute,bin/traceroute.db} (complain)
/usr/sbin/smbldap-useradd (complain)
/usr/sbin/smbldap-useradd///etc/init.d/nscd (complain)
/usr/sbin/smbd (complain)
/usr/sbin/nscd (complain)
/usr/sbin/nmbd (complain)
/usr/sbin/mdnsd (complain)
/usr/sbin/identd (complain)
/usr/sbin/dovecot (complain)
/usr/sbin/dnsmasq (complain)
/usr/sbin/dnsmasq//libvirt_leaseshelper (complain)
/usr/sbin/avahi-daemon (complain)
/usr/lib/dovecot/ssl-params (complain)
/usr/lib/dovecot/pop3-login (complain)
/usr/lib/dovecot/pop3 (complain)
/usr/lib/dovecot/managesieve-login (complain)
/usr/lib/dovecot/managesieve (complain)
/usr/lib/dovecot/log (complain)
/usr/lib/dovecot/lmtp (complain)
/usr/lib/dovecot/imap-login (complain)
/usr/lib/dovecot/imap (complain)
/usr/lib/dovecot/dovecot-lda (complain)
/usr/lib/dovecot/dovecot-lda///usr/sbin/sendmail (complain)
/usr/lib/dovecot/dovecot-auth (complain)
/usr/lib/dovecot/dict (complain)
/usr/lib/dovecot/deliver (complain)
/usr/lib/dovecot/config (complain)
/usr/lib/dovecot/auth (complain)
/usr/lib/dovecot/anvil (complain)
/usr/lib/chromium-browser/chromium-browser (complain)
/usr/lib/chromium-browser/chromium-browser//xdgsettings (complain)
/usr/lib/chromium-browser/chromium-browser//sanitized_helper (enforce)
/usr/lib/chromium-browser/chromium-browser//lsb_release (complain)
/usr/lib/chromium-browser/chromium-browser//chromium_browser_sandbox (complain)
/usr/lib/chromium-browser/chromium-browser//browser_openjdk (enforce)
/usr/lib/chromium-browser/chromium-browser//browser_java (enforce)
syslogd (complain)
syslog-ng (complain)
klogd (complain)
ping (complain)
docker-default (enforce)
/usr/sbin/ntpd (enforce)
/usr/sbin/tcpdump (enforce)
/usr/lib/connman/scripts/dhclient-script (enforce)
/usr/lib/NetworkManager/nm-dhcp-helper (enforce)
/usr/lib/NetworkManager/nm-dhcp-client.action (enforce)
/sbin/dhclient (enforce)
/usr/lib/snapd/snap-confine (enforce)
/usr/lib/snapd/snap-confine//mount-namespace-capture-helper (enforce)
man_groff (enforce)
man_filter (enforce)
/usr/bin/man (enforce)
```

## AppArmor Profile

### `apparmor-deny-write`

```
profile apparmor-deny-write flags=(attach_disconnected) {
    file,
    # Deny all file writes.
    deny /** w, 
}
```

### `apparmor-deny-proc-write`

```
profile apparmor-deny-proc-write flags=(attach_disconnected) {
    file,
    # Deny all file writes to /proc.
    deny /proc/* w,
}
```

### `apparmor-deny-remount-root`

```
profile apparmor-deny-remount-root flags=(attach_disconnected) {
    # Deny remount readonly the root filesystem.
    deny mount options=(ro, remount) -> /, 
}
```

## AppArmor Status

```shell
aa-status
```

```
apparmor module is loaded.
52 profiles are loaded.
15 profiles are in enforce mode.
   /sbin/dhclient
   /usr/bin/man
   /usr/lib/NetworkManager/nm-dhcp-client.action
   /usr/lib/NetworkManager/nm-dhcp-helper
   /usr/lib/chromium-browser/chromium-browser//browser_java
   /usr/lib/chromium-browser/chromium-browser//browser_openjdk
   /usr/lib/chromium-browser/chromium-browser//sanitized_helper
   /usr/lib/connman/scripts/dhclient-script
   /usr/lib/snapd/snap-confine
   /usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/sbin/ntpd
   /usr/sbin/tcpdump
   docker-default
   man_filter
   man_groff
37 profiles are in complain mode.
   /usr/lib/chromium-browser/chromium-browser
   /usr/lib/chromium-browser/chromium-browser//chromium_browser_sandbox
   /usr/lib/chromium-browser/chromium-browser//lsb_release
   /usr/lib/chromium-browser/chromium-browser//xdgsettings
   /usr/lib/dovecot/anvil
   /usr/lib/dovecot/auth
   /usr/lib/dovecot/config
   /usr/lib/dovecot/deliver
   /usr/lib/dovecot/dict
   /usr/lib/dovecot/dovecot-auth
   /usr/lib/dovecot/dovecot-lda
   /usr/lib/dovecot/dovecot-lda///usr/sbin/sendmail
   /usr/lib/dovecot/imap
   /usr/lib/dovecot/imap-login
   /usr/lib/dovecot/lmtp
   /usr/lib/dovecot/log
   /usr/lib/dovecot/managesieve
   /usr/lib/dovecot/managesieve-login
   /usr/lib/dovecot/pop3
   /usr/lib/dovecot/pop3-login
   /usr/lib/dovecot/ssl-params
   /usr/sbin/avahi-daemon
   /usr/sbin/dnsmasq
   /usr/sbin/dnsmasq//libvirt_leaseshelper
   /usr/sbin/dovecot
   /usr/sbin/identd
   /usr/sbin/mdnsd
   /usr/sbin/nmbd
   /usr/sbin/nscd
   /usr/sbin/smbd
   /usr/sbin/smbldap-useradd
   /usr/sbin/smbldap-useradd///etc/init.d/nscd
   /usr/{sbin/traceroute,bin/traceroute.db}
   klogd
   ping
   syslog-ng
   syslogd
4 processes have profiles defined.
4 processes are in enforce mode.
   /sbin/dhclient (599) 
   docker-default (6565) 
   docker-default (8572) 
   docker-default (8720) 
0 processes are in complain mode.
0 processes are unconfined but have a profile defined.
```

## AppArmor Modes

| Mode | Description |  
|:-:|:-|  
| enforce | 허용되지 않는 리소스에 대한 액세스 차단 |  
| complain | 위반 사항만 보고 |  
| unconfined | 리소스에 대한 액세스를 차단하지 않는다 |

## AppArmor Profiles 생성

### `~/add_data.sh`

```shell
#!/bin/bash
data_directory=/opt/app/data
mkdir -p ${data_directory}
echo "=> File created at `date`" | tee ${data_directory}/create.log
```

```shell
~/add_data.sh
```

```
=> File created at Mon Mar 12 03:29:22 UTC 2021
```

```shell
cat /opt/app/data/create.log
```

```
=> File created at Mon Mar 12 03:29:22 UTC 2021
```

### `apparmor-utils` 설치

```shell
apt-get install -y apparmor-utils
```

### Profile 생성

```shell
aa-genprof ~/add_data.sh
```

### Default AppArmor Profiles Directory

```shell
ll /etc/apparmor.d/
```

### Apply Profile

```shell
apparmor_parser -q /etc/apparmor.d/usr.sbin.nginx-updated
```

