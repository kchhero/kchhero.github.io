---
title: 'Trouble Shooting'
layout: post
tags:
  - yocto
category: yocto
---
#### Yocto Trouble shooting

##### IMAGE_ROOTFS_SIZE 관련
build 후 생성되는 ext4 image를 ext2simg 를 이용하여 sparse image로 변환시키면 아래처럼 BLOCK SIZE가 
1K 로 잡힌다. ==> 0x0400 으로 읽어야함.

0000000: 3aff26ed 01000000 1c000c00 **00040000**  :.&.............
0000010: 50cf0100 0e000000 00000000 c1ca0000  P...............
0000020: 01000000 0c040000 00000000 00000000  ................

**IMAGE_ROOTFS_SIZE 의 default값은 8192**

./meta/recipes-core/images/core-image-tiny-initramfs.bb:24:IMAGE_ROOTFS_SIZE = "8192"
./meta/recipes-core/images/core-image-minimal-initramfs.bb:19:IMAGE_ROOTFS_SIZE = "8192"
./meta/recipes-core/images/core-image-minimal.bb:11:IMAGE_ROOTFS_SIZE ?= "8192"

tiny image type으로 build시 rootfs size가 작게 잡히면서 default ROOTFS_SIZE가 적용되고, 
.ext4 생성시 block size 역시 default 1K 로 잡히는듯하다. poky 분석을 더 해봐야함.

아무튼 IMAGE_ROOTFS_SIZE를 명시적으로 65536 이상으로 크게 설정하게 되면 아래처럼 header BLOCK_SIZE는
4K 로 잡히게 된다. sparse image 를 fasboot 로 flash 할때 중요하다.

0000000: 3aff26ed 01000000 1c000c00 **00100000**  :.&.............
0000010: 00dc1c00 0e000000 00000000 c1ca0000  ................
0000020: 00800000 0c000008 00000000 00000000  ................