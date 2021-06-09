---
title: "Ubuntu Swap Disable"

date: 2021-06-04T02:03:35+09:00

categories:

- linux
- ubuntu
- swap

tags:

- linux
- ubuntu
- swap
- fstab

keywords:

- linux
- ubuntu
- swap
- fstab

---

## 컴퓨터에서 Swap 이란??

{{<admonition note SWAP true>}}  
메모리 안에서 데이터를 페이지나 세크먼트로 교체하는것이다.  
스와핑은 메인 메모리보다 큰 다양한 데이터 파일들이나 실행 프로그램들을 화성화 하기 위한 유용한 기술이다.  
더 자세한 사항은 [여기로!](https://www.webopedia.com/definitions/swap/)  
{{</admonition>}}

## Show Swap

```shell
free
```

```
              total        used        free      shared  buff/cache   available
Mem:        2035240      683076      448960        1900      903204     1195404
Swap:             0           0           0
```

```shell
sudo swapon -show
```

```
Filename				Type		Size	Used	Priority
/swap.img                              	file    	1794044	0	-2
```

## 현재 세션 비활성화

```shell
sudo swapoff -a
```

## 영구적인 비활성화

```shell
sudo vi /etc/fstab
```

```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-9P6wxgVviPOldHd8McXwnECNFYGbJQXIcJX3JQbySwOeEHhF09z1mRNWyWSVb8jb / ext4 defaults 0 0
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/5afd959f-a436-4e5e-8ae5-456f52e6cffe /boot ext4 defaults 0 0
# Below change to comment!!
#/swap.img      none    swap    sw      0       0
```

