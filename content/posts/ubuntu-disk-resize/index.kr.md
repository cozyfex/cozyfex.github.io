---
title: "Ubuntu Disk Resize"

date: 2021-06-05T16:22:36+09:00

author: CozyFex

categories:

- linux
- ubuntu
- disk
- resize

tags:

- linux
- ubuntu
- disk
- resize
- fdisk
- parted
- pvresize
- lvextend
- resize2fs
- df

keywords:

- linux
- ubuntu
- disk
- resize
- fdisk
- parted
- pvresize
- lvextend
- resize2fs
- df

---

## Device 와 Disk Names 확인

```shell
sudo fdisk -l
```

```
GPT PMBR size mismatch (20971519 != 62914559) will be corrected by write.
The backup GPT table is not on the end of the device. This problem will be corrected by write.
Disk /dev/sda: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: FE6441A3-322D-411A-8EB2-1EAD23D3BB20

Device       Start      End  Sectors Size Type
/dev/sda1     2048     4095     2048   1M BIOS boot
/dev/sda2     4096  2101247  2097152   1G Linux filesystem
/dev/sda3  2101248 20969471 18868224   9G Linux filesystem


Disk /dev/mapper/ubuntu--vg-ubuntu--lv: 8.102 GiB, 9659482112 bytes, 18866176 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

{{<admonition note "Check!">}}  
`GPT PMBR size mismatch (20971519 != 62914559) will be corrected by write.` 를 확인 할 수 있다.  
{{</admonition>}}

| Type | Value |  
|:-:|:-|  
| Device | `/dev/sda3` |  
| Disk | `/dev/mapper/ubuntu--vg-ubuntu--lv` |

## 파티션 넘버 확인

```shell
sudo parted
```

```
GNU Parted 3.3
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print list
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sda: 32.2GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name  Flags
 1      1049kB  2097kB  1049kB                     bios_grub
 2      2097kB  1076MB  1074MB  ext4
 3      1076MB  32.2GB  31.1GB


Model: Linux device-mapper (linear) (dm)
Disk /dev/mapper/ubuntu--vg-ubuntu--lv: 31.1GB
Sector size (logical/physical): 512B/512B
Partition Table: loop
Disk Flags:

Number  Start  End     Size    File system  Flags
 1      0.00B  31.1GB  31.1GB  ext4


(parted) quit
```

| Prompt | Input | Action |  
|:-:|:-:|:-|  
| (parted) | `print list` | Print partition list |  
| (parted) | `quit` | Quit parted program |

{{<admonition note "Check!">}}  
`Disk Flags` 섹션 확인.  
{{</admonition>}}

## 파티션 크기 변경

```shell
sudo parted
```

```
GNU Parted 3.3
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) resizepart
Warning: Not all of the space available to /dev/sda appears to be used, you can fix the GPT to
use all of the space (an extra 41943040 blocks) or continue with the current setting?
Fix/Ignore? Fix
Partition number? 3
End?  [10.7GB]? 100%
(parted) quit
Information: You may need to update /etc/fstab.
```

| Prompt | Input | Action |  
|:-:|:-:|:-|  
| (parted) | `resizepart` | Resize partition |  
| Fix/Ignore? | `Fix` | Resize partition |  
| Partition number? | `3` | Select partition number |  
| End? | `100%` | Set partition size |  
| (parted) | `quit` | Quit parted program |

## 물리적 볼륨 리사이즈

```shell
sudo pvresize /dev/sda3
```

```
  Physical volume "/dev/sda3" changed
  1 physical volume(s) resized or updated / 0 physical volume(s) not resized
```

## 논리적 볼륨 크기 확장

```shell
sudo lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
```

```
  Size of logical volume ubuntu-vg/ubuntu-lv changed from <9.00 GiB (2303 extents) to <29.00 GiB (7423 extents).
  Logical volume ubuntu-vg/ubuntu-lv successfully resized.
```

## 파일 시스템 리사이즈

```shell
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```

```
resize2fs 1.45.5 (07-Jan-2020)
Filesystem at /dev/mapper/ubuntu--vg-ubuntu--lv is mounted on /; on-line resizing required
old_desc_blocks = 2, new_desc_blocks = 4
The filesystem on /dev/mapper/ubuntu--vg-ubuntu--lv is now 7601152 (4k) blocks long.
```

## 변경 확인!

```shell
df -h
```

```
Filesystem                         Size  Used Avail Use% Mounted on
udev                               951M     0  951M   0% /dev
tmpfs                              199M  2.4M  197M   2% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   29G  8.3G   19G  31% /
tmpfs                              994M     0  994M   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
tmpfs                              994M     0  994M   0% /sys/fs/cgroup
/dev/loop0                          56M   56M     0 100% /snap/core18/1944
/dev/loop1                          56M   56M     0 100% /snap/core18/2066
/dev/sda2                          976M  200M  710M  22% /boot
/dev/loop2                          70M   70M     0 100% /snap/lxd/19188
/dev/loop3                          68M   68M     0 100% /snap/lxd/20326
/dev/loop4                          32M   32M     0 100% /snap/snapd/10707
/dev/loop5                          33M   33M     0 100% /snap/snapd/12057
tmpfs                              199M     0  199M   0% /run/user/1000
```

{{<admonition note "Check!">}}  
`/dev/mapper/ubuntu--vg-ubuntu--lv` 가 `29G` 인것을 확인 할 수 있다.  
{{</admonition>}}