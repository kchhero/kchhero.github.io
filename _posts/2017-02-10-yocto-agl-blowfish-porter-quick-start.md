---
title: 'AGL blowfish porter board quick start'
layout: post
tags: yocto
category: Yocto
---
##### 1. Source Download

```
~/AGL$ repo init -u https://gerrit.automotivelinux.org/gerrit/AGL/AGL-repo -b blowfish -m default_blowfish_2.0.1.xml
~/AGL$ repo sync
```

##### 2. Driver Download

```
https://www.renesas.com/ko-kr/solutions/automotive/rcar-demoboard.html
 ==> 여기에서 Graphics library와 multimedia 관련 linux driver 를 download 받아야함.
(아래 2개)아래의 file 을 받았으면 적당한 위치에 이동 (ex. /home/temp)
R-Car_Series_Evaluation_Software_Package_for_Linux-20151228.zip
R-Car_Series_Evaluation_Software_Package_of_Linux_Drivers-20151228.zip
```

##### 3. Script 실행

```
2번에서 download 받은 .zip file의 압축을 풀고 meta-renesas 의 해당 recipes dir 에 copy하는 scripts.
~/AGL/r-car-agl/meta-renesas/meta-rcar-gen2$ ./copy_gfx_software_silk.sh /home/temp
~/AGL/r-car-agl/meta-renesas/meta-rcar-gen2$ ./copy_mm_software_lcb.sh /home/temp
~/AGL/r-car-agl/meta-renesas/meta-rcar-gen2$ ./copy_gfx_software_porter.sh /home/temp
```

##### 4. recipe 수정

```
 meta-rcar-gen2/conf/machine/porter.conf  에서 아래처럼 ext3 type 추가
 IMAGE_FSTYPES += "tar.bz2 ext3"
```

##### 5. Build

```
~/AGL/r-car-agl$ source meta-agl/scripts/aglsetup.sh -m porter -b build agl-devel agl-demo agl-appfw-smack
~/AGL/r-car-agl/build$ bitbake agl-demo-platform
```

##### 6. SD card download

```
~/AGL/r-car-agl/build$ cd tmp/deploy/images/porter
~/AGL/r-car-agl/build/tmp/deploy/images/porter$ mkdir rootfs
~/AGL/r-car-agl/build/tmp/deploy/images/porter$ sudo mount agl-demo-platform-porter-20160912111051.rootfs.ext3 rootfs
~/AGL/r-car-agl/build/tmp/deploy/images/porter$ sudo cp uImage+dtb rootfs/boot
~/AGL/r-car-agl/build/tmp/deploy/images/porter$ sudo umount rootfs
~/AGL/r-car-agl/build/tmp/deploy/images/porter$ sudo dd if=agl-demo-platform-porter.ext3 of=/dev/sdd1 bs=4M
```

##### 7. Booting

```
Board Name : RCAR-M2-PORTER-A
board 뒷편에 sdcard slot 있음. mouse 장착해야 booting 후 application 실행 가능한 상태가 됨.
uboot 설정
    # setenv bootargs_console 'console=ttySC6,38400 ignore_loglevel'
    # setenv bootargs_video 'vmalloc=384M video=HDMI-A-1:1920x1080-32@60'
    # setenv bootargs_root 'root=/dev/mmcblk0p1 rootdelay=3 rw rootfstype=ext3 rootwait'
    # setenv bootmmc '1:1'
    # setenv bootcmd_sd 'ext4load mmc ${bootmmc} 0x40007fc0 boot/uImage+dtb'
    # setenv bootcmd 'setenv bootargs ${bootargs_console} ${bootargs_video} ${bootargs_root}; run bootcmd_sd; bootm 0x40007fc0'
```

##### 8. Running CES 2016 Demos

```
Board Reset/Booting
$ export LD_PRELOAD=/usr/lib/libEGL.so
$ cd /opt/AGL/CES2016
/opt/AGL/CES2016$ /usr/bin/qt5/qmlscene -–fullscreen -I imports Main.qml
/opt/AGL/CES2016$ ./switch_to_ivi-shell.sh
/opt/AGL/CES2016$ ./start_CES2016_ivi-shell.sh
or
/opt/AGL/ALS2016$ ./switch_to_ivi-shell.sh
/opt/AGL/ALS2016$ ./start_ALS2016_with_navi_ivi-shell.sh
```

---

##### 참고자료
[AGL-Kickstart-on-Renesas-Porter-board.pdf](https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwitwNLf-4TSAhWHoJQKHW0JC1cQFggYMAA&url=http%3A%2F%2Fiot.bzh%2Fdownload%2Fpublic%2F2016%2Fsdk%2FAGL-Kickstart-on-Renesas-Porter-board.pdf&usg=AFQjCNEp10zyPt0_lG5gWa5yuWEM9yPTcg&sig2=ktdL0c2z26UYEGIdFLSgVw "AGL-Kick")

---
