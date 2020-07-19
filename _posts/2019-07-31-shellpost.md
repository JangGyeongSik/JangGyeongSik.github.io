---
title: "ShellScript(1) "
date: 2019-07-31 17:45
categories: Linux Ubuntu Shell
---
#ShellScripts-1 


```class
cat data.txt

hong 28 011-222-2222 seoul 
park 34 017-333-3333 kyunggi 
im 23 019-444-4444 chungnam 
son 49 016-555-5555 us 
gil 19 018-666-6666 korea
jang 21 011-7777-7777 japan 
lee 16 016-8888-8888 china 
sa 45 017-9999-9999 canada 
hwang 32 015-555-5555  kwangju
```

```shell
cut -c : 문자수 반환
user@user-B85M-DS3H-A:~$ cut -c 2-4 data.txt
ong
ark
m 2
on 
il 
ang
ee 
a 4
wan
cut -d : 문자열 구분, 공백문자 구분시 " " 삽입, f는 구분자로 몇번째 Field인지 선택, 여러 필드 선택가능
cut는 일정한 공백을 " " 로 잘 구분해줘야함 

(실습) etc/passwd 파일을 출력하는데 유저와 PID만 위에서부터 5개 출력해라.
user@user-B85M-DS3H-A:~$ cat /etc/passwd | cut -d ":" -f1,3 | head -5
root:!
daemon:*
bin:*
sys:*
sync:*
user@user-B85M-DS3H-A:~$ cat /etc/passwd | cut -d ":" -f1,3-5 | head -5
이렇게 표기할 수 있음
```
```class
------------------------------------------------------------------------------------------------------------------------
paste : 파일 합치기 , 새로운 데이터를 만들기 위해 사용한다

user@user-B85M-DS3H-A:~$ cat > exam1
red
blue
white
^C
user@user-B85M-DS3H-A:~$ cat > exam2
yellow
green
gray
black
^C
user@user-B85M-DS3H-A:~$ paste exam1 exam2		[ default : 세워서 옆으로 합침 ]
red	yellow
blue	green
white	gray
	black
user@user-B85M-DS3H-A:~$ paste -d ":" exam1 exam2 	[ - d : 구분자로 합침 ]
red:yellow
blue:green
white:gray
:black
user@user-B85M-DS3H-A:~$ paste -s exam1 exam2 / 		[ -s : 눕혀서 위 아래로 합침 ]
red	blue	white
yellow	green	gray	black
------------------------------------------------------------------------------------------------------------------------
```

```shell
------------------------------------------------------------------------------------------------------------------------
grep : 파일 내 텍스트 검색
user@user-B85M-DS3H-A:~$ grep -v hong data.txt 	[-v : 제외할 단어 ]
park 34 017-333-3333 kyunggi 
im 23 019-444-4444 chungnam 
son 49 016-555-5555 us 
gil 19 018-666-6666 korea
jang 21 011-7777-7777 japan 
lee 16 016-8888-8888 china 
sa 45 017-9999-9999 canada 
hwang 32 015-555-5555 kwangju
user@user-B85M-DS3H-A:~$ grep -i HonG data.txt 	[-i :  대소문자 구분 안함 ]
hong 28 011-222-2222 seoul 
user@user-B85M-DS3H-A:~$ grep -n hong data.txt 	[-n : 라인넘버 출력]
1:hong 28 011-222-2222 seoul 

user@user-B85M-DS3H-A:~$ grep -l hong data.txt 	[ -l : 파일명 ]
data.txt

user@user-B85M-DS3H-A:~$ grep -c park data.txt 	[ -c : 지정한 텍스트를 사용한 유저가 몇명 ]
1
```

