---
title: "vsftpd Restrict User Directory Access"

date: 2021-04-09T11:54:00+0900

author: CozyFex

categories:

- linux
- ftp

tags:

- linux
- ftp
- vsftpd

keywords:

- linux
- ftp
- vsftpd

---

## vsftp Config

### Modify config file

```bash
vi /etc/vsftpd/vsftpd.conf
```

```vim
---
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
allow_writeable_chroot=YES
---
```

{{<admonition note "chroot_local_user" true>}}  
Restrict change directory to top level, user can move in user's directory.  
{{</admonition>}}

{{<admonition note "chroot_list_enable" true>}}  
If it's YES - The user who registered below file can move to top level.  
If it's NO - The user who registered below file can not move to top level.  
{{</admonition>}}

{{<admonition note "chroot_list_file" true>}}  
The user list is on the file.(One line must have one user id)  
{{</admonition>}}

{{<admonition note "allow_writeable_chroot" true>}}  
If you set `chroot_local_user` is YES, and you want to write file on the account, you have to
set `allow_writeable_chroot` is YES.  
{{</admonition>}}

### vsftpd Restart

```bash
service vsftpd restart
```
