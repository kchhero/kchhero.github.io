---
layout: post
tags:
  - linux
title: 'ubuntu Tips'
category: linux
---
##### sudo passwd 묻지 않기
```shell?line_number=false
$ sudo nano /etc/sudoers.d/sukersudo
==> suker ALL=NOPASSWD:POWEROFF,SHUTDOWN,HALT,/usr/bin/update-manager,/usr/bin/apt-get,/bin/mount,/bin/cp,/bin/mv,/bin/umount
```

##### display /dev/fb size 알아내기
```shell?line_number=false
$ fbset
```

#####  /dev/fb0 동작확인
```shell?line_number=false
$ cat /dev/urandom > /dev/fb0
```

##### ifconfig은 내부 IP만 보임. 외부 IP 확인은 sudo wget -q http://ip.kiduk.kr && more index.html
    112.216.64.90

##### 디렉토리별 size확인
```shell?line_number=false
     du -sh *
```