---
title: "vsftpd 상위 디렉토리 접근 제한"

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

## vsftp 설정

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
계정 홈 디렉토리 상위 폴더 이동 제한  
{{</admonition>}}

{{<admonition note "chroot_list_enable" true>}}  
설정이 YES - 아래 설정한 파일에 등록 된 사용자만 이동 제한 해제  
설정이 NO - 아래 설정한 파일에 등록 된 사용자멘 이동 제한  
{{</admonition>}}

{{<admonition note "chroot_list_file" true>}}  
사용자 목록 작성(한줄에 아이디 하나씩)  
{{</admonition>}}

{{<admonition note "allow_writeable_chroot" true>}}  
`chroot_local_user`를 YES로 설정하면 해당 계정으로 파일 쓰기를 위해서는 `allow_writeable_chroot`를 YES로 설정해야 한다.  
{{</admonition>}}

## vsftpd 재시작

```bash
service vsftpd restart
```
