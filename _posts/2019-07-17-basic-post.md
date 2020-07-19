---
title: "Linux Class day6 "
date: 2019-07-17 17:30
categories: Linux
toc: true
toc_sticky: true
toc_label: 목차
---

## Chapter 2-3 리눅스 Swap, LV,PV  

## Chapter 1. Linux Swap
```bash
---------------------------swap----------------------------
file -s /dev/sdd*
mkswap /dev/sdd1
mkdir /swapdir
man dd
dd if=/dev/zero of=/swapdir/swapfile bs=512 count=1048576 -> 내용은없고 특수한용도로 쓰인다. 
ls -l /dev/zero

file -s /swapdir/swapfile -> swap 확인 
swapon -s  -> swap하는 공간을 알려주는 명령어 
swapon -a
swapon -s
swapon /dev/sdd1
swapon /swapdir/swapfile

chmod 600 /swapdir/swapfile -> 권한을 소유자한테만준다.
swapon -s 
-스왑 되잇는 process 확인.

file -s /swapdir/swapfile
UUID 값 확인후에 
vim /etc/fstab
UUID="ID값" swap swap defaults 0 0
/디렉토리명/파일명 swap swap defaults 0 0
```

## 2. LVM 생성
```bash
-------------논리볼륨 생성 ------------
fdisk /dev/sdb,c,d,e,f

ID System
8e Linux LVm

lvmdiskscan -> 물리볼륨확인
pvcreate /dev/sdb1 ->물리볼륨추가
pvdisplay 물리볼륨 정보 확인

pvdisplay -C -> 대략적인 정보만 보여준다.

pvdisplay -c -> 각각의 항목들 사이의값을 추출해서 보여줌.

pvdisplay -s -> 어느정도의 공간이 확보되있는지 알려준다

pvremove  /장치/폴더 -물리볼륨제거 

vgdisplay ->볼륨 그룹 display

vgcreate vg_test /장치/폴더 -> 볼륨그룹 만들기 PE Size(default = 4.00MB ) 

vgremove vg_test /장치/폴더 -> 볼륨그룹 remove 

lvcreate -n lv_test -L 100M vg_test -> 크기를 사이즈로 지정( -L)

lvcreate -n lv_test_2 -l 10 vg_test -> PE를 개수로 지정 (-l)

ls -l /dev/vg_test -> list 보여줌
ls -l /dev/dm* -> dm list 보여줌 

Disk ㅡㅡㅡㅡㅡ> RAM 
Program vs Process
	성능 용량 내 결함성
0-stripe n   n	   x
i-mirror 1   1     n-1
5-distribute Parity n-1 n-1 1
6-이중   n-2  n-2   2

lvcreate -n stripe_lv -L 500M -i 3 -I 64 vg_test
```

## 3. RAID-1과 동일한 방식

```bash
lvcreate -n mirror_lv -L 100M -m 2 vg_test
-m 옵션에 중복개수 2개 사용, 이 미러 볼륨은 2개의 중복을 생성 물리볼륨에 데이터를 중복 기록

lvcreate -n mirror_lv2 -L 100M -m 3 vg_test
사용할 물리볼륨 개수보다 많은 중복을 지정하려고 할 경우 오류발생.

lvcreate --type raid5 -L 100M -i 2 -I 64K -n raid5_lv vg_test


pvs, vgs, lvs -> 각각의 볼륨 어떤 공간을쓰는지 간단하게 표현

RAID1 - 중첩 레이드, 


lvremove /dev/vg_test/raid5_lv -> 이 과정 진행후에 경고가 뜰수있다. 
vgreduce --removemissing vg_test -> 삭제하기위해서 vg_test를 빼내준다. 
vgremove vg_test  

vgcreate vg_test /dev/sdb1 /dev/sddc1 /dev/sdd1 /dev/sde1
lvcreate --type raid10 -L 100M -i 2 -m 1 -n raid10_lv vg_test
pvs

lvcreate -T -L 100M vg_test/thinpool -> 씬 프로비저닝구성 
물리볼륨 -> 볼륨 그룹 -> 논리볼륨에서 '물리볼륨' -> 볼륨그룹 -> 씬풀 -> 논리볼륨

lvcreate -T -V 10T -n thin_lv vg_test/thinpool
-V -> 가상의 Byte 적용 

vgextend vg_test


mkfs.xfs /dev/vg_test/test_lv
mount /dev/vg_test/test_lv /lvtest
df -h /lvtest

lvextend -L +100M /dev/vg_test/test_lv
df -h /lvtest

lvreduce -L 200M /dev/vg_test/lv2 
```


실습예제 
주의
1) 실습 전 기존 /etc/fstab 파일에 추가로 등록한 항목을 삭제하고 진행하시오.
2) 기존 실습에 사용했던 디스크를 virtualbox 설정에서 제거하고, 각 10GB 크기를 가지는 두 개의 디스크를 새롭게 추가하시오.

1. 새롭게 추가된 디스크를 각각 MBR 및 GPT 방식의 디스크로 초기화 하시오.
```bash
fdisk /dev/sdb
gdisk /dev/sdc -> Linux LVM ( 8e00 )
```

2. MBR 방식으로 초기화 된 디스크를 각 파티션이 2GB 정도의 용량을 가지도록 파티셔닝 하시오.
```bash
fdisk /dev/sdb -> p -> n -> default -> +2GB 
```
3. GPT 방식으로 초기화 된 디스크를 5GB씩 용량의 두 파티션으로 분할하시오.
이 때, 각 파티션을 Linux LVM 방식의 파티션으로 설정하시오.
```bash
gdisk /dev/sdc -> Linux LVM ( 8e00 )
```

4. MBR 방식으로 초기화 된 디스크의 각 파티션에 대하여 ext2, ext3, ext4, xfs, fat 방식으로 파일시스템을 생성하시오.
```bash
mkfs -t ext2 /dev/sdb1
mkfs -t ext3 /dev/sdb2
mkfs -t ext4 /dev/sdb3
mkfs -t xfs  /dev/sdb5
mkfs -t fat  /dev/sdb6
```
4번은 extended 이기때문에 확장파티션이라 건들 ㄴ ㄴ 

5. 4에서 생성된 각 파일시스템을 마운트하기 위하여 /filesystem 디렉토리를 생성하고, 각 파일시스템 종류의 이름 별 디렉토리를 생성하시오.
(ex: /filesystem/ext2)
```bash
mkdir -p /filesystem/{ext2,ext3,ext4,xfs,fat}
```
6. mount 명령을 사용하여 각 파일시스템 별 디렉토리에 장치를 마운트 하시오.
```bash
mount -t ext2 /dev/sdb1 /filesystem/ext4
```
7. ext4, xfs 파일시스템을 가지고 있는 디스크는 시스템 부팅 시 자동으로 마운트 되도록 설정하시오.
(주의: mount -a 명령을 사용하여 테스트 할 것)
```bash
vim /etc/fstab

UUID="ID값"	/filesystem/ext4 ext4 defaults 0 0
UUID="ID값"	/filesystem/xfs  xfs  defaults 0 0

/dev/sdb3: UUID="25fba2ad-0f27-4fec-93ab-53f8c3547867" TYPE="ext4"
/dev/sdb5: UUID="cce179db-36af-4c4b-9a2d-aad5104a61b8" TYPE="xfs"  
```
