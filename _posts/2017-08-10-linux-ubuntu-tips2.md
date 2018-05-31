---
categories: Uncategoried
category: Uncategoried
layout: post
tags:
  - linux
title: 'ubuntu Tips2'
---
#### oracle-java8-installer 설치 문제
Errors were encountered while processing:  oracle-java8-installer
jdk-8u144-linux-x64.tar.gz download fail

/var/cache/oracle-java8-installer/jdk*.tar.gz  를 확인해보면
정상적으로 pacake download가 되지 않고 있다.
수동으로 pacakage를 받아서 설치 하는 방법은 아래와 같다.
```
$ wget -c --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz
$ sudo cp jdk-8u144-linux-x64.tar.gz /var/cache/oracle-jdk8-installer/
$ sudo apt install oracle-java8-installer
```

<br>

#### E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing? 
==> sudo apt-get upgrade --fix-missing

<br>

#### 특정포트를 사용하는 PID 확인 방법
```
netstat -anp | grep LISTEN | grep :포트번호
실행예시
[root@zetawiki ~]# netstat -anp | grep LISTEN | grep :80
tcp        0      0 :::80                       :::*                        LISTEN      19055/httpd

sudo 로 실행하지 않는 경우 PID가 안보일 수 있다.
```

<br>

#### ubuntu에서 CD 구울때
```
$ sudo apt-get install wodim
$ cdrecord -v {path_to_isoFile}
```

<br>

#### mount 방법
```
sudo mount -t cifs -o user="suker",password="cndgus123" //192.168.1.16/suker ~/sukerSMB'
sudo mount -t cifs //NAS/1st ~/Nexell-NAS1 -o  user=nexell,password=nexell,workgroup=WORKGROUP,ip=192.168.1.17,iocharset=utf8'
sudo mount -t cifs //NAS/2nd ~/Nexell-NAS2 -o user=nexell,password=nexell,workgroup=WORKGROUP,ip=192.168.1.17,iocharset=utf8'
sudo mount -t cifs //NAS/3rd ~/Nexell-NAS3 -o user=nexell,password=nexell,workgroup=WORKGROUP,ip=192.168.1.17,iocharset=utf8'
sudo mount /dev/sdb1 /home/suker/sukerSDB
 ```

<br>

#### /etc/udev/rules.d/ 수정/추가 후 logout 하지 않고 device 등록하는 방법
```
$ sudo udevadm control --reload-rules
```

<br>

#### 나의 외부 IP 확인 방법
```
$ curl ifconfig.me
```

<br>

#### smb server 연결 문제
Windows samba server 연결시 (ubuntu 18.04에서) Unable access to server 발생시 아래와 같이 수정하면 된다.
```
$ sudo vim /etc/samba/smb.conf

 추가 ==> client max protocol = NT1

$ sudo service smbd restart
```

<br>

#### bcompare "Trial Mode error" 발생시 registry.dat 를 삭제한다.
```
$ rm ~/.config/bcompare/registry.dat
```