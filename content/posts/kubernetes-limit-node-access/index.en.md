---
title: "Kubernetes Limit Node Access"

date: 2021-07-04T18:52:44+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- limit node access

keywords:

- kubernetes
- security
- limit node access

---

## Rules

* Create Cluster in Private Network
* The Cluster(Private Network) is NOT ALLOWED public network.
* The Cluster(Private Network) can ACCESS VPN.
* The Cluster(Private Network) can ACCESS specific IP ranges.
* The Cluster(Private Network) can ACCESS who is needed.

## Server Account Types

### User Accounts

{{<admonition note "User Accounts" true>}}  
This is for normal user for ftp, ssh, etc.  
{{</admonition>}}

### Superuser Account

{{<admonition note "Superuser" true>}}  
This is mean, typically `root` user.  
It's UID = 0.  
{{</admonition>}}

### System Accounts

{{<admonition note "System" true>}}  
This is for system programs.  
`sys`, `mail`
{{</admonition>}}

### Service Accounts

{{<admonition note "Service" true>}}  
This is for services.  
`nginx`, `http`
{{</admonition>}}

## Check Points for Server Accounts

### Current User

```shell
id
```

```shell
who
```

```shell
last
```

### Check Account Files

```shell
cat /etc/passwd
```

```shell
cat /etc/shadow
```

```shell
cat /etc/group
```

### Configure User

#### User Mode

```shell
usermod –s /bin/nologin michael
```

#### User Delete

```shell
userdel bob
```

#### Exclude the User from a Group

```shell
id michael
```

```
uid=1001(michael) gid=1001(michael) groups=1001(michael),1000(admin)
```

```shell
 deluser michael admin 
```

```
Removing user `michael` from group `admin` ...
Done.
```

```shell
id michael
```

```
uid=1001(michael) gid=1001(michael) groups=1001(michael)
```


