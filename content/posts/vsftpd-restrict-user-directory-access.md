---
title: "Vsftpd Restrict User Directory Access"

date: 2021-04-09T11:54:00+0900

author: CozyFex

categories:

- vsftpd

tags:

- vsftpd

keywords:

- vsftpd

---

## vsftp Config
----

##### Modify config file / 설정파일 수정

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

<span style="color: white">**chroot_local_user**</span>\
Restrict change directory to top level, user can move in user's directory.\
계정 홈 디렉토리 상위 폴더 이동 제한
<br/><br/>
<span style="color: white">**chroot_list_enable**</span>\
If it's YES - The user who registered below file can move to top level.\
If it's NO - The user who registered below file can not move to top level.\
설정이 YES - 아래 설정한 파일에 등록 된 사용자만 이동 제한 해제\
설정이 NO - 아래 설정한 파일에 등록 된 사용자멘 이동 제한
<br/><br/>
<span style="color: white">**chroot_list_file**</span>\
The user list is on the file.(One line must have one user id)\
사용자 목록 작성(한줄에 아이디 하나씩)
<br/><br/>
<span style="color: white">**allow_writeable_chroot**</span>\
If you set **chroot_local_user** is YES, and you want to write file on the account, you have to set **
allow_writeable_chroot** is YES.
**chroot_local_user**를 YES로 설정하면 해당 계정으로 파일 쓰기를 위해서는 **allow_writeable_chroot**를 YES로 설정해야 한다.

<br/>
##### vsftpd Restart
```bash
service vsftpd restart
```
