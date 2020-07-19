---
title: "Linux Class day5 "
date: 2019-07-16 17:10
categories: Linux
toc: true
toc_sticky: true
toc_label: 목차
---

## Chapter2-2 

```class
-고급권한관리

1.확장 권한 종류

setuid, setgid, sticky bit
setuid : binary file(2진수) or shell scripts , 실행 가능한 파일 목적 ) 권한을 주기 위함.
```

```bash
[root@localhost ~]# ls -l /etc/shadow
	-r--------. 1 root root 1996 Jul 15 02:20 /etc/shadow
	<-- 비밀번호 변경 후 -->	
	[root@localhost ~]# ls -l /etc/shadow
	-r--------. 1 root root 1996 Jul 15 02:42 /etc/shadow

[root@localhost ~]# ls -l /bin/passwd
	-rwsr-xr-x. 1 root root 27832 Jun 10  2014 /bin/passwd
		-rws 에서 s가 setuid를 의미한다. 실행하는 동안만은 소유자의 권한을 사용, 명령어 끝내면 	권한 반납
	[root@localhost ~]# ls -l /bin/ls
	-rwxr-xr-x. 1 root root 117680 Oct 30  2018 /bin/ls
```

```bash
setgid
[userab@localhost ~]$ /tmp/passwd
Changing password for user userab.
Changing password for userab.
(current) UNIX password: 
passwd: Authentication token manipulation error , setuid가 적절히 생성되지 않았을 때 에러

[root@localhost userabc]# chown root:root /tmp/passwd
[root@localhost userabc]# ls -l /tmp/passwd
-rwxr-xr-x. 1 root root 27832 Jul 15 02:51 /tmp/passwd
[root@localhost userabc]# chmod u+s /tmp/passwd
[root@localhost userabc]# ls -l /tmp/passwd
-rwsr-xr-x. 1 root root 27832 Jul 15 02:51 /tmp/passwd

setuid, setgid , sticky bit =  setuid 인 경우
1	0	0	    =  
[root@localhost userabc]# chmod 4755 /tmp/passwd
[root@localhost userabc]# ls -l /tmp/passwd
-rwsr-xr-x. 1 root root 27832 Jul 15 02:51 /tmp/passwd

1	1	0	    =  setuid 와 setgid를 사용하고 Sticky bit를 설정 안한다. 
[root@localhost userabc]# chmod 6755 /tmp/passwd
[root@localhost userabc]# ls -l /tmp/passwd
-rwsr-sr-x. 1 root root 27832 Jul 15 02:51 /tmp/passwd

=> 

[root@localhost ~]# chmod g+s /test
[root@localhost ~]# chown adm:sys /test
[root@localhost ~]# ls -ld /test
drwxrwsrwx. 2 adm sys 22 Jul 15 03:01 /test
[root@localhost ~]# exit
logout
[userab@localhost ~]$ touch /test/userfile2
[userab@localhost ~]$ ls -l /test/userfile2
-rw-r--r--. 1 userab sys 0 Jul 15 03:04 /test/userfile2
[userab@localhost ~]$ ls -l /test/userfile*
-rw-r--r--. 1 userab user2000 0 Jul 15 03:01 /test/userfile
-rw-r--r--. 1 userab sys      0 Jul 15 03:04 /test/userfile2

소유자는 그대로지만 소유그룹은 디렉토리 권한을 chown으로 넣어줘서 sys로 들어감
언제쓰느냐 → 

[root@localhost ~]# mkdir /share
[root@localhost ~]# chmod 777 /share
[root@localhost ~]# su - user
[user@localhost ~]$ touch /share/user1.txt
[user@localhost ~]$ exit
logout
[root@localhost ~]# su - Centos
Last login: Thu Jul 11 01:00:50 EDT 2019 on pts/1
[Centos@localhost ~]$ touch /share/Centos.txt
[Centos@localhost ~]$ exit
logout
[root@localhost ~]# ls -l /share
total 0
-rw-rw-r--. 1 Centos Centos 0 Jul 15 03:07 Centos.txt
-rw-rw-r--. 1 user   user   0 Jul 15 03:06 user1.txt
[root@localhost ~]# chgrp sys /share/*
[root@localhost ~]# ls -l /share
total 0
-rw-rw-r--. 1 Centos sys 0 Jul 15 03:07 Centos.txt
-rw-rw-r--. 1 user   sys 0 Jul 15 03:06 user1.txt
– setgid 를 바꿈

[root@localhost ~]# chmod u+s /test
[root@localhost ~]# ls -ld /test
drwsrwsrwx. 2 adm sys 39 Jul 15 03:04 /test
[root@localhost ~]# exit
logout
[userab@localhost ~]$ touch /test/userfile3
[userab@localhost ~]$ ls -l /test
total 0
-rw-r--r--. 1 userab user2000 0 Jul 15 03:01 userfile
-rw-r--r--. 1 userab sys      0 Jul 15 03:04 userfile2
-rw-r--r--. 1 userab sys      0 Jul 15 03:09 userfile3
	-setuid는 디렉토리에 영향을 주지 못한다.
```


Sticky bit : 파일에 대해서 권한을 다 제거 했는데도 파일이 지워지는 이유는?
->파일을 지우거나 만들수 있는 권한은 파일이 있는 share라는 경로가 결정함


```bash
[userab@localhost ~]$ touch /share/secret.txt
[userab@localhost ~]$ chmod 000 /share/secret.txt
[userab@localhost ~]$ ls -l /share/secret.txt 
----------. 1 userab user2000 0 Jul 15 03:11 /share/secret.txt
[userab@localhost ~]$ su -
Password: 
[root@localhost ~]# su - Centos
Last login: Mon Jul 15 03:07:00 EDT 2019 on pts/1
[Centos@localhost ~]$ ls -l /share/
total 0
-rw-rw-r--. 1 Centos sys      0 Jul 15 03:07 Centos.txt
----------. 1 userab user2000 0 Jul 15 03:11 secret.txt
-rw-rw-r--. 1 user   sys      0 Jul 15 03:06 user1.txt
[Centos@localhost ~]$ cat /share/secret.txt 
cat: /share/secret.txt: Permission denied
[Centos@localhost ~]$ rm /share/secret.txt 
rm: remove write-protected regular empty file ‘/share/secret.txt’? y
[Centos@localhost ~]$ ls -l /share/
total 0
-rw-rw-r--. 1 Centos sys 0 Jul 15 03:07 Centos.txt
-rw-rw-r--. 1 user   sys 0 Jul 15 03:06 user1.txt

->이렇게봤듯이 내 파일이 아닌 다른사람이 만든 파일을 지우지 못한다!
```

```bash
[Centos@localhost ~]$ ls -ld /tmp
drwxrwxrwt. 19 root root 4096 Jul 15 03:12 /tmp
	파일을 지우는것은 파일을 만든 사람만이 지울 수 있도록 한 것이 스티키 비트.
	파일에 대한 내용 수정은 파일 자체권한 , 삭제 또는 손상은 파일을 생성하려는 디렉토리의 권한
	심볼릭 방식으로 설정 가능하고 8진수로 설정하려면 4자릿수로 설정해야 함
```
