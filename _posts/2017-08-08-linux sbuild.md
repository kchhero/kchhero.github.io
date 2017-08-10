---
title: sbuild
layout: post
tags:
  - linux
category: Uncategoried
---
#### sbuild, schroot

##### sbuild install
```
$ sudo apt-get install dh-autoreconf dh-systemd
$ sudo apt install sbuild debhelper ubuntu-dev-tools
$ sbuild-update --keygen
   keygen fail시 --> $ sudo apt-get install rng-tools
```

<br>

#### 참고
#### **https://wiki.ubuntu.com/SimpleSbuild**

chroot name parameter로 사용되는 부분
**/etc/schroot/chroot.d/sbuild-xenial-amd64-armhf**

**$ sudo mk-sbuild --target armhf xenial**
--> 아래에 chroot 가 생성된다.
**/var/lib/schroot/chroots/xenial-amd64-armhf/**

<br>

#### Trouble Shooting
##### libbluetooth3
E: Failed to fetch http://localhost:47273//libbluetooth3_5.42-0ubuntu1.1_armhf.deb  Writing more data than expected (57912 > **57884**)
E: Failed to fetch http://localhost:47273//libbluetooth-dev_5.42-0ubuntu1.1_armhf.deb  Writing more data than expected (151256 > 151170)
E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
E: Failed to process build dependencies

-->

./debs/bluez_5.42-0ubuntu1.1_armhf.changes:61: bb28bb0693760fd613ad93bc66f3e9deb4c1737e798f548f238fb155c4ece8ad **57884** libbluetooth3_5.42-0ubuntu1.1_armhf.deb

해당 파일을 검색해보면 아래와 같이 apt-cacher-ng 에 (my hostPC) 존재하는데, 
file size가 위 log 처럼 /var/cache 의 file이 57912 이다. sbuild 시 사용하는 libbluetooth3 는 57884 다.
```
$ locate libbluetooth3_5.42-0ubuntu1.1_armhf.deb
/home/suker/artik/build-artik/output/images/artik530/SUKER-1AA-00001/20170809.000001/debs/libbluetooth3_5.42-0ubuntu1.1_armhf.deb
/var/cache/apt-cacher-ng/localhost/libbluetooth3_5.42-0ubuntu1.1_armhf.deb
/var/cache/apt-cacher-ng/localhost/libbluetooth3_5.42-0ubuntu1.1_armhf.deb.head
/var/cache/apt-cacher-ng/localhost/debs/libbluetooth3_5.42-0ubuntu1.1_armhf.deb
/var/cache/apt-cacher-ng/localhost/debs/libbluetooth3_5.42-0ubuntu1.1_armhf.deb.head
```

E: Failed to fetch http://download.tizen.org/tools/latest-release/Ubuntu_16.04/Packages  404  Not Found
E: Failed to fetch http://dl.google.com/linux/chrome/deb/dists/stable/main/binary-amd64/Packages  404  Not Found
E: Failed to fetch http://kr.archive.ubuntu.com/ubuntu/dists/xenial-backports/restricted/binary-amd64/Packages  503  Cache storage error - No such file or directory
E: Failed to fetch http://www.scootersoftware.com/dists/bcompare4/non-free/binary-amd64/Packages  404  Not Found
E: Failed to fetch http://kr.archive.ubuntu.com/ubuntu/dists/xenial/main/binary-amd64/Packages  503  Cache storage error - No such file or directory
E: Failed to fetch http://security.ubuntu.com/ubuntu/dists/xenial-security/main/binary-amd64/Packages  503  Cache storage error - No such file or directory
E: Failed to fetch http://ppa.launchpad.net/umang/indicator-stickynotes/ubuntu/dists/xenial/main/binary-amd64/Packages  503  Cache storage error - No such file or directory
E: Failed to fetch http://ppa.launchpad.net/webupd8team/java/ubuntu/dists/xenial/main/binary-amd64/Packages  503  Cache storage error - No such file or directory
E: Failed to fetch http://kr.archive.ubuntu.com/ubuntu/dists/xenial-updates/main/binary-amd64/Packages  503  Cache storage error - No such file or directory
E: Some index files failed to download. They have been ignored, or old ones used instead.

sudo /etc/init.d/apt-cacher-ng restart

<br>

##### Could not connect to 127.0.0.1
1. ps -aux | grep bind  로 찾아서 pid kill

