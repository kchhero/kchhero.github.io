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