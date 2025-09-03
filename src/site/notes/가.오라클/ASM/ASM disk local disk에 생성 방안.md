---
{"dg-publish":true,"permalink":"/가.오라클/ASM/ASM disk local disk에 생성 방안/","dgPassFrontmatter":true,"noteIcon":""}
---

#### CreateDate : 2025-09-01

### GOAL
운영 DB의 ASM DISKGROUP을 스토리지 스냅샷 기술로 복제하여 Single GRID 구성된 DR 또는 백업 DB에서 ASM Disk mount 하려고 함.
이를 위해 DR 및 백업 DB에 GRID 엔진 설치 및 ohas daemon, ASM 인스턴스 구성을 하고자 하나 사용할 수 있는 여유 디스크가 없기 때문에 "파일과 loopback device"를 활용하여 block device를 생성한 후
이를 활용한 Single GRID 구성 방법을 다루고자 함.

### Tested Env, APPLIES TO:
Oracle Enterprise Linux 8.10
Oracle Database - Enterprise Edition - Version 23.4 -> 23.7

### SOLUTION

~~~
파일을 활용한 block device 생성
-- ASM disk로 사용할 2G사이즈의 파일 생성(사실 사이즈는 ASM spfile/pwfile 정도만 저장되는 ASM Diskgroup 이므로 더 작아도 상관 없음)

[root@ol76 ~]$ dd if=/dev/zero of=asm_disk1.file bs=1M count=2048

2048+0 records in
2048+0 records out

2147483648 bytes (2.1 GB) copied, 3.62633 s, 592 MB/s

[root@ol76 ~]$ ll asm_disk1.file

-rw-r--r-- 1 grid dba 2147483648 Apr 28 14:54 asm_disk1.file
-- loopback 디바이스 중에 미사용 디바이스 번호 알아내기 (사용중인 디바이스는 다음 명령어에서 조회된다)

[root@ol76 ~]$ cat /proc/mounts | grep /dev/loop
-- 앞서 만든 파일을 /dev/loop0 디바이스에 연결 (위에서 조회된 번호 제외하고 선택)
[root@ol76 ~]# losetup /dev/loop0 /ora02/asm_disk1.file
[root@ol76 ~]# ll /dev/loop0
brw-rw---- 1 root disk 7, 0 Apr 28 14:56 /dev/loop0
-- loopback 디바이스 구성 확인

[root@ol76 ~]# lsblk

NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT

sdb 8:16 0 20G 0 disk
└─sdb1 8:17 0 20G 0 part
sr0 11:0 1 1024M 0 rom
loop0 7:0 0 2G 0 loop <---------- 생성확인
sdc 8:32 0 20G 0 disk
└─sdc1 8:33 0 20G 0 part
sda 8:0 0 100G 0 disk
├─sda2 8:2 0 99G 0 part
│ ├─ol-swap 252:1 0 6G 0 lvm [SWAP]
│ └─ol-root 252:0 0 93G 0 lvm /
└─sda1 8:1 0 1G 0 part /boot

-- 원래는 udev, ASMlib, FilterDriver등을 사용하여 ownership, permission을 설정해야 하지만 임시로 사용하고 버릴 디스크이므로 chown chmod로 변경해준다.

[root@ol76 /]# chown oracle:dba /dev/loop0
[root@ol76 /]# chmod 660 /dev/loop0
[root@ol76 /]# ll /dev/loop0
brw-rw---- 1 grid dba 7, 0 Apr 28 14:56 /dev/loop0


나중에 GRID 설치가 끝난 후 필요없어지면 "losetup -d /dev/loop0" 명령어로 위의 디바이스를 제거한다.
~~~
### REFERENCES
- 인터넷 어딘가..


#loopback #asm #loopback 