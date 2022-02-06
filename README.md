# FS_3.5
# 1
Sparse файл – файл, в котором последовательности нулевых байтов заменены на информацию об этих последовательностях. 
# 2
Не могут иметь разные права доступа и владельца, т.к. имеют один и тот же Inode
# 4
Разбил диск sdb
```
vagrant@vagrant:~$ vagrant@vagrant:~$ sudo fdisk -l /dev/sdb
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xb7fe5395

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 4196351 4194304    2G 83 Linux
/dev/sdb2       4196352 5242879 1046528  511M  5 Extended
```
# 5
```
root@vagrant:~# sfdisk -d /dev/sdb | sfdisk /dev/sdc
Checking that no-one is using this disk right now ... OK

Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Created a new DOS disklabel with disk identifier 0xb7fe5395.
/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.
/dev/sdc2: Created a new partition 2 of type 'Extended' and of size 511 MiB.
/dev/sdc3: Done.

New situation:
Disklabel type: dos
Disk identifier: 0xb7fe5395

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M  5 Extended

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
root@vagrant:~# fdisk -l /dev/sdb /dev/sdc
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xb7fe5395

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 4196351 4194304    2G 83 Linux
/dev/sdb2       4196352 5242879 1046528  511M  5 Extended


Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xb7fe5395

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M  5 Extended
root@vagrant:~#
```
# 6
```
root@vagrant:~# mdadm --create R1 -l 1 -n 2 /dev/sdb1 /dev/sdc1
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array?
Continue creating array? (y/n) y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md/R1 started.
root@vagrant:~# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop  /snap/core18/2128
loop1                       7:1    0 70.3M  1 loop  /snap/lxd/21029
loop2                       7:2    0 32.3M  1 loop  /snap/snapd/12704
loop3                       7:3    0 55.5M  1 loop  /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop  /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md127                   9:127  0    2G  0 raid1
└─sdb2                      8:18   0    1K  0 part
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md127                   9:127  0    2G  0 raid1
└─sdc2                      8:34   0    1K  0 part
```
# 7
```
root@vagrant:~# mdadm --create R0 -l 0 -n 2 /dev/sdc2 /dev/sdb2
mdadm: partition table exists on /dev/sdc2
mdadm: partition table exists on /dev/sdc2 but will be lost or
       meaningless after creating array
mdadm: partition table exists on /dev/sdb2
mdadm: partition table exists on /dev/sdb2 but will be lost or
       meaningless after creating array
Continue creating array?
Continue creating array? (y/n) y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md/R0 started.
root@vagrant:~# cat /proc/mdstat
Personalities : [raid1] [linear] [multipath] [raid0] [raid6] [raid5] [raid4] [raid10]
md126 : active raid0 sdb2[1] sdc2[0]
      1042432 blocks super 1.2 512k chunks

md127 : active (auto-read-only) raid1 sdc1[1] sdb1[0]
      2094080 blocks super 1.2 [2/2] [UU]

unused devices: <none>
```
# 8
```
root@vagrant:~# pvcreate /dev/md127
  Physical volume "/dev/md127" successfully created.
root@vagrant:~# pvs
  PV         VG        Fmt  Attr PSize   PFree
  /dev/md127           lvm2 ---   <2.00g  <2.00g
  /dev/sda3  ubuntu-vg lvm2 a--  <63.00g <31.50g
root@vagrant:~# pvcreate /dev/md126
  Physical volume "/dev/md126" successfully created.
root@vagrant:~# pvs
  PV         VG        Fmt  Attr PSize    PFree
  /dev/md126           lvm2 ---  1018.00m 1018.00m
  /dev/md127           lvm2 ---    <2.00g   <2.00g
  /dev/sda3  ubuntu-vg lvm2 a--   <63.00g  <31.50g
  ```
  # 9
  ```
  root@vagrant:~# vgcreate vg1 /dev/md127 /dev/md126
  Volume group "vg1" successfully created
root@vagrant:~# vgs
  VG        #PV #LV #SN Attr   VSize   VFree
  ubuntu-vg   1   1   0 wz--n- <63.00g <31.50g
  vg1         2   0   0 wz--n-  <2.99g  <2.99g
  ```
  # 10
  ```
  root@vagrant:~# pvs
  PV         VG        Fmt  Attr PSize    PFree
  /dev/md126 vg1       lvm2 a--  1016.00m 1016.00m
  /dev/md127 vg1       lvm2 a--    <2.00g   <2.00g
  /dev/sda3  ubuntu-vg lvm2 a--   <63.00g  <31.50g
root@vagrant:~# lvcreate -L 100M vg1 /dev/md126
  Logical volume "lvol0" created.
root@vagrant:~# lvs
  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  ubuntu-lv ubuntu-vg -wi-ao----  31.50g
  lvol0     vg1       -wi-a----- 100.00m
  ```
  # 11
  ```
  root@vagrant:~# mkfs.ext4 /dev/vg1/lvol0
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done
Writing inode tables: done
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```
# 12
```
root@vagrant:~# mount /dev/vg1/lvol0 /tmp/new
root@vagrant:~# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.5M  1 loop  /snap/core18/2284
loop1                       7:1    0 67.2M  1 loop  /snap/lxd/21835
loop2                       7:2    0 55.4M  1 loop  /snap/core18/2128
loop3                       7:3    0 61.9M  1 loop  /snap/core20/1328
loop4                       7:4    0 70.3M  1 loop  /snap/lxd/21029
loop5                       7:5    0 43.4M  1 loop  /snap/snapd/14549
loop6                       7:6    0 32.3M  1 loop  /snap/snapd/12704
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md127                   9:127  0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
  └─md126                   9:126  0 1018M  0 raid0
    └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md127                   9:127  0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
  └─md126                   9:126  0 1018M  0 raid0
    └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new
root@vagrant:~# lvs
  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  ubuntu-lv ubuntu-vg -wi-ao----  31.50g
  lvol0     vg1       -wi-ao---- 100.00m
root@vagrant:~# df -lh
Filesystem                         Size  Used Avail Use% Mounted on
udev                               447M     0  447M   0% /dev
tmpfs                               99M 1008K   98M   2% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   31G  3.7G   26G  13% /
tmpfs                              491M     0  491M   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
tmpfs                              491M     0  491M   0% /sys/fs/cgroup
/dev/loop1                          68M   68M     0 100% /snap/lxd/21835
/dev/loop0                          56M   56M     0 100% /snap/core18/2284
/dev/loop4                          71M   71M     0 100% /snap/lxd/21029
/dev/loop2                          56M   56M     0 100% /snap/core18/2128
/dev/loop5                          44M   44M     0 100% /snap/snapd/14549
/dev/loop3                          62M   62M     0 100% /snap/core20/1328
/dev/loop6                          33M   33M     0 100% /snap/snapd/12704
/dev/sda2                          976M  107M  803M  12% /boot
tmpfs                               99M     0   99M   0% /run/user/1000
/dev/mapper/vg1-lvol0               93M   72K   86M   1% /tmp/new
root@vagrant:~#
```
# 13
```
root@vagrant:~# cd /tmp/new/
root@vagrant:/tmp/new# wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
--2022-02-04 23:25:31--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22144566 (21M) [application/octet-stream]
Saving to: ‘/tmp/new/test.gz’

/tmp/new/test.gz                                  100%[==========================================================================================================>]  21.12M  15.1MB/s    in 1.4s

2022-02-04 23:25:32 (15.1 MB/s) - ‘/tmp/new/test.gz’ saved [22144566/22144566]
```
# 14
```
root@vagrant:/tmp/new# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.5M  1 loop  /snap/core18/2284
loop1                       7:1    0 67.2M  1 loop  /snap/lxd/21835
loop2                       7:2    0 55.4M  1 loop  /snap/core18/2128
loop3                       7:3    0 61.9M  1 loop  /snap/core20/1328
loop4                       7:4    0 70.3M  1 loop  /snap/lxd/21029
loop5                       7:5    0 43.4M  1 loop  /snap/snapd/14549
loop6                       7:6    0 32.3M  1 loop  /snap/snapd/12704
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md127                   9:127  0    2G  0 raid1
└─sdb2                      8:18   0  511M  0 part
  └─md126                   9:126  0 1018M  0 raid0
    └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md127                   9:127  0    2G  0 raid1
└─sdc2                      8:34   0  511M  0 part
  └─md126                   9:126  0 1018M  0 raid0
    └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new
```
# 15
```
root@vagrant:/tmp/new# gzip -t test.gz
root@vagrant:/tmp/new# echo $?
0
```
# 16
```
root@vagrant:/tmp/new# pvmove /dev/md126 /dev/md127
  /dev/md126: Moved: 48.00%
  /dev/md126: Moved: 100.00%
root@vagrant:/tmp/new# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.5M  1 loop  /snap/core18/2284
loop1                       7:1    0 67.2M  1 loop  /snap/lxd/21835
loop2                       7:2    0 55.4M  1 loop  /snap/core18/2128
loop3                       7:3    0 61.9M  1 loop  /snap/core20/1328
loop4                       7:4    0 70.3M  1 loop  /snap/lxd/21029
loop5                       7:5    0 43.4M  1 loop  /snap/snapd/14549
loop6                       7:6    0 32.3M  1 loop  /snap/snapd/12704
sda                         8:0    0   64G  0 disk
├─sda1                      8:1    0    1M  0 part
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk
├─sdb1                      8:17   0    2G  0 part
│ └─md127                   9:127  0    2G  0 raid1
│   └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new
└─sdb2                      8:18   0  511M  0 part
  └─md126                   9:126  0 1018M  0 raid0
sdc                         8:32   0  2.5G  0 disk
├─sdc1                      8:33   0    2G  0 part
│ └─md127                   9:127  0    2G  0 raid1
│   └─vg1-lvol0           253:1    0  100M  0 lvm   /tmp/new
└─sdc2                      8:34   0  511M  0 part
  └─md126                   9:126  0 1018M  0 raid0
  ```
# 17

```
root@vagrant:/tmp/new# mdadm /dev/md127 -f /dev/sdc1
mdadm: set /dev/sdc1 faulty in /dev/md127
root@vagrant:/tmp/new# mdadm --detail /dev/md127
/dev/md127:
           Version : 1.2
     Creation Time : Fri Feb  4 22:22:14 2022
        Raid Level : raid1
        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Sun Feb  6 17:43:34 2022
             State : clean, degraded
    Active Devices : 1
   Working Devices : 1
    Failed Devices : 1
     Spare Devices : 0

Consistency Policy : resync

              Name : vagrant:R1  (local to host vagrant)
              UUID : 42999888:42f78c1e:213db259:8b0e12e6
            Events : 24

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       -       0        0        1      removed

       1       8       33        -      faulty   /dev/sdc1
```
# 18
```
root@vagrant:/tmp/new# dmesg
...
  [ 5321.372501] md/raid1:md127: Disk failure on sdc1, disabling device.
               md/raid1:md127: Operation continuing on 1 devices.
```
# 19
```
root@vagrant:/tmp/new# gzip -t /tmp/new/test.gz
root@vagrant:/tmp/new# echo $?
0
```
