---
title: "Disk Management"
date: 2019-07-30 17:50
categories: Linux
toc: true
toc_sticky: true
toc_label: 목차 
---
 ## Disk Management
```bash
parted 
fdisk -l 하면 추가한 하드디스크가 나오지 않음, 아직 논리적 장치로 인식을 못해서
```
```class
트랙 : 섹터가 모여서 하나의 원을 생성
실린더 : 여러 동심 트랙에 대한 스택 구조
파티션 : 실린더의 모임
파티션을 할때는 실린더 단위로 하는 것이 좋다
LVM : 디스크를 계속 붙일 수 있어서 사용함
```

```bash
pvcreate /dev/sdd
vgcreate vg_test1 /dev/sdd
lvcreate -n lv_test1 -L +512MB vg_test1
	-만들때는 vg_test에 대한 경로가 생성되지 않았으니 뒤에 볼륨이름을 설정해주는 것
삭제할때는 lvremove [lv경로]
lvextend -L 100 /dev/vg_test1/lv_test1 -r	// r 이 붙어야 마운트지점도 같이 증가
lvreduce -L 100 /dev/vg_test1/lv_test1
	- 이미 만들어진 논리볼륨에 대해서 경로가 생성되었으니 경로로 작성
mkfs.xfs /dev/vg_test1/lv_test1
mkdir /mnt/test_dir
mount /dev/vg_test1/lv_test1 /mnt/test_dir 
```

- 위처럼 xfs로 파일시스템을 만들고 마운트를 시켰을때 lvreduce가 불가능함, xfs은 확장만 가능함
- lvmdiskscan 말고 df -h로 계속 확인해줍시다.  

```bash
ls -l /proc/mounts
ls -l /etc/mtab

cat /proc/net/tcp
netstat -an | more

cat /var/run/sshd.pid
netstat -anpt | grep :22 //  22번 포트 확인		netstat -nr : 라우팅 테이블 확인, u는 udp
ls -l /proc/1936/exe
cat /proc/net/tcp

ps -ef | grep ssh[d]	// 프로세스와 서비스의 상관관계를 알 수 있는 예제
cat /var/run/sshd.pid	// pid확인 , 위의 PID와 일치
ls -l /proc/1936/exe	// /proc/1936/exe -> /usr/sbin/sshd
netstat -antp | grep 1936
lsof -p 1936

which lsof => 경로 출력
rpm -qf [which losf의 경로] => 설치된 패키지 명 출력
```

## Daemon 
1. run level  
2. super daemon   
3. standalone   
--> 3가지 방식이 있다.
```class
1. etc/rc.d/rc0~6 : 런레벨을 불러오는 장소 0(halt) 1(single mode) 6(reboot) 5(graphical_mode) 3(multiuser_mode) , runlevel 또는 who -r로 확인
2. super daemon = inetd -> xinetd 	자원이 부족할때 사용하던 방식이여서 요즘 잘 안씀
3. standalone = 개별적으로 사용자가 동작시키는 것 , systemctl restart sshd.service 
ls -ld /etc/rc.d/rc.local => 부팅시 실행될 것, 데몬 포함
profile = 로그인시 적용, etc보다 빨리 적용
```


## 작업 스케줄러

1회성 작업 : at 데몬 : at -l.atq.atrm [jobnumber] (at데몬 삭제)
```bash
cd /var/spool/at

date
at 09:46
echo hi >/dev/pts/2
at -l
cd /var/spool/at
ls
ls -l a864522572
atq
atrm 3
at -l
atq
```

## crontab
- crontab : 컴퓨터 꺼지면 무효
- 0 , 7 =  일요일을 의미
```bash
*/5 */4 * * 1 command
```
```class
crontab -r 은 절대 하지 않는다
anacron은 실행하지 않을때 다시 실행하도록 함
check기능
```
```bash
anacrontab 확인
cat /etc/anacrontab 
```
