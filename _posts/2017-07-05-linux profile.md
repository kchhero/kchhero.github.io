---
title: 'profile 관련'
layout: post
tags:
  - linux
category: linux
---
####/etc/profile

이전까지는 부팅시 실행하는 script나 환경 변수 설정을 굳이 systemd 의 service 에 추가하였는데, 
이렇게 하면 복잡하기만 할 뿐, 단순 script 실행은 profile.d 를 이용하면 된다.

/etc/profile 은 다음과 같다.
```
(snip)
16 if [ -d /etc/profile.d ]; then
17         for i in /etc/profile.d/*.sh; do
18                 if [ -f $i -a -r $i ]; then
19                         . $i
20                 fi
21         done
22         unset i
23 fi
(snip)
```
/etc/profile.d 에 존재하는 .sh 를 실행시킨다.

<br>

---

aaa
