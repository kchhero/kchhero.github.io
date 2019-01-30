---
layout: post
tags:
  - Yocto
title: 'ubuntu rootfs porting2'
category: yocto
---
#### Yocto - Ubuntu rootfs porting work diary

앞서 만들었던 ubuntu image rootfs를 만들때 xenial version의 경우 apt update 가 잘 동작하지 않는 문제가 있다.
USB ethernet 연결도 매끄럽지 못하다.
try3 의 내용에 약간의 수정사항이 있다.

---

### try 3 - 2

```
$ sudo debootstrap --arch=armhf --foreign --include=ubuntu-keyring,apt-transport-https,ca-certificates,openssl trusty "ubuntu-rootfs" http://ports.ubuntu.com

$ sudo cp /usr/bin/qemu-arm-static ubuntu-rootfs/usr/bin/

$ sudo chroot ubuntu-rootfs /bin/bash /debootstrap/debootstrap --second-stage

$ sudo echo "deb http://ports.ubuntu.com/ubuntu-ports/ trusty main restricted universe multiverse" >> ubuntu-rootfs/etc/apt/sources.list
$ sudo echo "deb http://ports.ubuntu.com/ubuntu-ports/ trusty-updates main restricted universe multiverse" >> ubuntu-rootfs/etc/apt/sources.list
$ sudo echo "deb http://ports.ubuntu.com/ubuntu-ports/ trusty-security main restricted universe multiverse" >> ubuntu-rootfs/etc/apt/sources.list

$ sudo chroot ubuntu-rootfs
$ adduser 'username'
$ gpasswd -a 'username' sudo
$ usermod -a -G sudo 'username'
$ sudo locale-gen "en_US.UTF-8"
$ sudo dpkg-reconfigure locales

home/nexell/.bashrc 에  추가  export LC_ALL="en_US.UTF-8"

$ echo "inet:x:3003:root" >> etc/group
$ echo "net_raw:x:3004:root" >> etc/group

# /etc/hosts 에 nexell-ubuntu 추가
# /etc/hostname 에 nexell-ubuntu 추가
# /etc/sudoers 에 nexell sudo권한 추가

etc/network/interfaces 에 추가

auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

$ apt-get update

apt-get install 실행시 E: Sub-process /usr/bin/dpkg returned an error code 발생한다면
아래의 설정후 chroot를 재실행해본다.

sudo mount -t proc proc ./myrootfs/proc/ sudo mount -t sysfs sys ./myrootfs/sys/
sudo mount -o bind /dev ./myrootfs/dev/
sudo mount -t devpts -o gid=5,mode=620 devpts ./ubuntu-rootfs/dev/pts


# apt-get install -y input-utils usbutils vim git wget
# apt-get install -y python-pip
# python -m pip install --upgrade pip setuptools wheel
# apt-get install -y libncurses5-dev
# apt-get install -y build-essential bin86 kernel-package 

# apt-get install -y openssh-client openssh-server

# apt-get install -y libpopt-dev locate binutils-dev libiberty-dev libcap-ng-utils

# cd home/nexell# git clone --recursive git://github.com/autotest/autotest.git
```

**
android-tools-adbd 관련 sub process failed message가 보인다면
```
rm -rf /var/lib/dpkg/info/android-tools-adbd.*
```
실행 후 다시 설치
