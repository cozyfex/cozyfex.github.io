---
title: "Linux CentOS Firewall"

date: 2021-05-17T17:43:32+09:00

categories:

- linux
- centos
- security
- firewall

tags:

- linux
- centos
- security
- firewall
- firewalld
- port

keywords:

- linux
- centos
- security
- firewall
- firewalld
- port

---

## What is `Firewalld`?

{{<admonition note firewalld true>}}  
`Firewalld` provides a dynamically managed firewall with support for network/firewall zones that define the trust level of network connections or interfaces.

It has support for IPv4, IPv6 firewall settings, ethernet bridges and IP sets.

There is a separation of runtime and permanent configuration options.

It also provides an interface for services or applications to add firewall rules directly.

You can check more details this link [Firewalld](https://firewalld.org/)  
{{</admonition>}}

## Install `Firewalld` and Run

```shell
# Install
yum install firewalld

# Start
systemctl start firewalld

# Enable
systemctl enable firewalld
```

## Configuration `Firewalld`

### Default Configuration Files Location

<sub>`/usr/lib/firewalld/`</sub>

```shell
cd /usr/lib/firewalld/
```

### System Specific Configuration Files Location

<sub>`/etc/firewalld/`</sub>

```shell
cd /etc/firewalld/
```

#### Application Configuration File

<sub>`/etc/firewalld/firewalld.conf`</sub>

```shell
vi /etc/firewalld/firewalld.conf
```

##### DefaultZone

<sub>DefaultZone</sub>

```shell
DefaultZone=public
```

##### Customized Configuration File

<sub>public</sub>

```shell
vi /etc/firewalld/zones/public.xml
```

<sub>/etc/firewalld/zones/public.xml</sub>

```xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
    <short>Public</short>
    <description>
        For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.
    </description>
    <service name="dhcpv6-client"/>
    <service name="http"/>
    <service name="ssh"/>
    <service name="https"/>
</zone>
```

<sub>custom</sub>

```shell
vi /etc/firewalld/zones/custom.xml
```

## Service Reload

```shell
firewall-cmd --reload
```

## Zone

### Predefined Zone List

```shell
firewall-cmd --get-zones
```

### All Zones Detail

```shell
firewall-cmd --list-all-zones
```

### Check Default Zone

```shell
firewall-cmd --get-default-zone
```

### Check Active Zone

```shell
firewall-cmd --get-active-zones
```

### Set Default Zone

```shell
firewall-cmd --set-default-zone=webserver
```

### Add New Zone

```shell
firewall-cmd --permanent --new-zone=webserver
```

### Delete Zone

```shell
firewall-cmd --permanent --delete-zone=webserver
```

## Service

### Service List

```shell
firewall-cmd --get-services
```

### Permanent Service List

```shell
firewall-cmd --permanent --list-all --zone=public
```

### Add Service

```shell
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --permanent --zone=public --add-service=https
```

### Remove Service

```shell
firewall-cmd --permanent --zone=public --remove-service=http
```

## Port

{{<admonition note Note true>}}  
After adding, changing, and deleting `Port`, you have to execute `--reload`.  
{{</admonition>}}

### Add Port

```shell
# One Port
firewall-cmd --permanent --zone=public --add-port=8080/tcp

# Range Ports
firewall-cmd --permanent --zone=public --add-port=4000-4100/tcp
```

### Remove Port

```shell
# One Port
firewall-cmd --permanent --zone=public --remove-port=8080/tcp

# Range Ports
firewall-cmd --permanent --zone=public --remove-port=4000-4100/tcp
```

## IP

{{<admonition note Note true>}}  
After adding, changing, and deleting `IP`, you have to execute `--reload`.  
{{</admonition>}}

### Add IP

```shell
firewall-cmd --permanent --zone=public --add-source=192.168.1.0/24 --add-port=22/tcp
```

### Change IP

```shell
firewall-cmd --permanent --zone=public --change-source=192.168.1.0/24
```

### Remove IP

```shell
firewall-cmd --permanent --zone=public --remove-source=192.168.1.0/24
```