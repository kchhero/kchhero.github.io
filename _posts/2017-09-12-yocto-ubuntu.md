---
title: 'ubuntu rootfs porting'
layout: post
tags:
  - yocto
category: yocto
---
#### Yocto - Ubuntu rootfs porting

yocto의 기본 OS system이 되는 oe 를 걷어내고 그 자리에 ubuntu 14.04 armhf binary로 대체하는 작업을 진행하였다.
작업 기간 2주 걸렸음.

---

##### try 1
처음에는 meta-nexell의 sub layer에 meta-debian 을 이용하여 package를 build 하고, nexell 자체 library 는 .deb 로 packaging 하려고 하였다. 
지난 7월부터 meta-nexell 을 krogoth에서 morty를 건너뛰고, pyro 버전으로 upgrade 하였는데, meta-debian 은 morty 까지 stable 인듯하다. build 자체에 문제가 많다.
하나씩 잡다가는 1년은 걸릴듯하여 skip 

---

##### try 2

ubuntu armhf tarball을 download 한 뒤, .deb 로 build 한 package 들을 dpkg -i 로 install 해 보았다.
일부 library 의 dependency 문제.. 특히 libc6 의 버전 문제가 상당히 어렵다. nx library 기준에 맞추어 libc6 를 dpkg 시키면
ubuntu 14.04 에 포함된 gstreamer1.0 에서 거부한다. 
adduser 와 같은 간단해보이는 package도 몇 가지 dependency 문제로 노가다가 예상되고(일일이 .deb 를 받아서 설치하는 데 드는 수고가 많다.), extension 에 약점을 보인다.
따라서 이 방법도 실패

---

##### try 3 
chroot + apt-get 로 성공함.
아래와 같은 step 으로 console mode 부팅 및 adbd 동작 까지 확인 되었다.

```
$ sudo debootstrap --arch=armhf --foreign --include=ubuntu-keyring,apt-transport-https,ca-certificates,openssl trusty "ubuntu-rootfs" http://ports.ubuntu.com

$ sudo cp /usr/bin/qemu-arm-static ubuntu-rootfs/usr/bin/
$ sudo chroot ubuntu-rootfs /bin/bash /debootstrap/debootstrap --second-stage

$ sudo echo "deb http://ports.ubuntu.com/ubuntu-ports/ trusty main restricted universe multiverse" >> ubuntu-rootfs/etc/apt/sources.list
$ sudo echo "deb http://ports.ubuntu.com/ubuntu-ports/ trusty-updates main restricted universe multiverse" >> ubuntu-rootfs/etc/apt/sources.list
$ sudo echo "deb http://ports.ubuntu.com/ubuntu-ports/ trusty-security main restricted universe multiverse" >> ubuntu-rootfs/etc/apt/sources.list

$ sudo chroot ubuntu-rootfs
$ sudo locale-gen "en_US.UTF-8"
$ sudo dpkg-reconfigure locales

$ apt-get update
$ apt-get install gstreamer1.0*
$ apt-get install libgles2-mesa-dev
$ apt-get install android-tools-adb android-tools-adbd

$ echo "inet:x:3003:root" >> etc/group
$ echo "net_raw:x:3004:root" >> etc/group
 
$ adduser nexell
$ gpasswd -a nexell sudo
$ usermod -a -G sudo nexell
```

<br>

unity desktop 설치는 아래와 같다.
```
$ sudo apt-get install ubuntu-desktop unity
$ openssh-server xserver-xorg-video-armsoc xserver-xorg-video-fbdev ==> desktop 은 login 문제로 아직 정상동작X
```

<br>

recipes-core/images/nexell-ubuntu.bb
```
inherit post-process-ubuntu 
include recipes-core/images/core-image-base.bb 

SPLASH = "" 
CORE_IMAGE_BASE_INSTALL = " \ 
${CORE_IMAGE_EXTRA_INSTALL} \ 
" 

LICENSE = "MIT" 
LIC_FILES_CHKSUM = "file://${COREBASE}/LICENSE;md5=4d92cd373abda3937c2bc47fbc49d690" 

IMAGE_LINGUAS = "" 

IMAGE_INSTALL_append = " \ 
packagegroup-nexell-ubuntu \ 
kernel-modules \ 
rtl-8188eus-${ARCH_TYPE_NUM} \ 
ubuntu-support \ 
testsuite-${NEXELL_BOARD_SOCNAME} \ 
"
```

<br>

마지막으로 생성한 rootfs.tar.gz 를 ext4 type의 rootfs.img 로 만들어 준다.
tools/convert_tools/mkrootfs_image.sh
```
#!/bin/bash 

#set -e 
# 1 target path ==> out/result path 
# 2 nexell-xxx-xxx.ext4 for convert sparse 
# 3 seek size 
# 4 support 

if [ "$TARGET_DIR" == "" ]; then 
TARGET_DIR=$1 
fi 

OUTPUT_NAME=$2 
ROOTFS_SIZE_M=$3 
EXTRA_ROOTFS=$4 

dd if=/dev/zero of=$TARGET_DIR/${OUTPUT_NAME} seek=${ROOTFS_SIZE_M} bs=1M count=0 

mkdir -p $TARGET_DIR/rootfs 
mkfs.ext4 -F -b 4096 -L rootfs $TARGET_DIR/${OUTPUT_NAME} 
sudo mount -o loop $TARGET_DIR/${OUTPUT_NAME} $TARGET_DIR/rootfs 
sudo tar xf $TARGET_DIR/rootfs.tar.gz -C $TARGET_DIR/rootfs 
sudo cp -rp ${EXTRA_ROOTFS}/* $TARGET_DIR/rootfs/ 

sync 

sudo umount $TARGET_DIR/rootfs 
e2fsck -y -f $TARGET_DIR/${OUTPUT_NAME}
```