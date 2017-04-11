---
category: linux
layout: post
tags:
  - linux
title: 'ubuntu Tips'
---
##### sudo passwd 묻지 않기
```shell?line_number=false
$ sudo nano /etc/sudoers.d/sukersudo
==> suker ALL=NOPASSWD:POWEROFF,SHUTDOWN,HALT,/usr/bin/update-manager,/usr/bin/apt-get,/bin/mount,/bin/cp,/bin/mv,/bin/umount
```

<br>

##### display /dev/fb size 알아내기
```shell?line_number=false
$ fbset
```

<br>

#####  /dev/fb0 동작확인
```shell?line_number=false
$ cat /dev/urandom > /dev/fb0
```

<br>

##### ifconfig은 내부 IP만 보임. 외부 IP 확인은 sudo wget -q http://ip.kiduk.kr && more index.html
    112.216.64.90

<br>

##### 디렉토리별 size확인
```shell?line_number=false
     du -sh *
```

<br>

##### chrome app shortcut 실행
shotcut binding 에 chrome의 jekyll app 실행을 추가한다. F9
[https://kchhero.github.io/programming/2017/04/10/python-shortcut-script.html](https://kchhero.github.io/programming/2017/04/10/python-shortcut-script.html)

F9는 "\e[20" 이다. 
```
--- .bashrc ---
# ---- suker keybinding -----
# <F8> run ~/asdf.sh
bind '"\e[19": "~/myshell.sh\n"'
bind '"\e[20": "~/jekylleditor.sh\n"'
```
1. 먼저 chrome tab에서 web app 화면으로 이동한다.
2. jekyll app  을 우클릭하여 Desktop shortcut으로 추가한다.
3. ~/Desktop/ 을 확인하면 다음과 같다.
```
$ cat ~/Desktop/chrome-dfdkgbhjmllemfblfoohhehdigokocme-Default.desktop
(snip)
Exec=/opt/google/chrome/google-chrome --profile-directory=Default --app-id=dfdkgbhjmllemfblfoohhehdigokocme
(snip)
```
4. 위 exec command를 F9에 연결하면 된다.
5. F9 를 눌러 실행하면 아래와 같은 error message가 나올 수 있다.
```shell
[11019:11019:0411/113710.197538:ERROR:browser_main_loop.cc(250)] GTK theme error: Unable to locate theme engine in module_path: "pixmap",
[11019:11019:0411/113710.198030:ERROR:browser_main_loop.cc(250)] GTK theme error: Unable to locate theme engine in module_path: "pixmap",
[11019:11019:0411/113710.266728:ERROR:edid_parser.cc(181)] invalid EDID: human unreadable char in name
[11019:11019:0411/113710.266923:ERROR:edid_parser.cc(181)] invalid EDID: human unreadable char in name
```
6. 아래 package를 설치해보자.
    sudo apt-get install  gtk2-engines-pixbuf:i386
