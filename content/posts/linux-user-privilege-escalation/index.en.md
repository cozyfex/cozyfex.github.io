---
title: "Linux User Privilege Escalation"

date: 2021-07-04T19:28:53+09:00

author: CozyFex

categories:

- linux
- escalation

tags:

- linux
- kubernetes
- escalation

keywords:

- linux
- kubernetes
- escalation

---

## SUDO

```shell
sudo visudo
# Or
sudo vi /etc/sudoers
```

```
# User privilege specification
root ALL=(ALL:ALL) ALL
# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL
# Allow members of group sudo to execute any command
%sudo ALL=(ALL:ALL) ALL
# Allow Bob to run any command
mark ALL=(ALL:ALL) ALL
# Allow Sarah to reboot the system
sarah localhost=/usr/bin/shutdown -r now
# See sudoers(5) for more information on "#include"
directives:
#includedir /etc/sudoers.d
```



