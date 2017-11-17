---
title: 'RISC-V BOOM core my Work history'
layout: post
tags:
  - RISC-V
category: Uncategoried
---
## RISC-V BOOM My work history

<br>

### 1. source download
```
$ repo init -u git://git.nexell.co.kr/nexell/riscv/manifest
$ repo sync
```

### 2. build guide
sifive source 를 기반으로 porting 및 customize 하는 부분은 생략. Nexell repository를 사용하는 방법에 대한 설명이다.

<Full build or first build>
```
$ cd riscv-boom-ref
$ tar xvzf buildroot.tar.gz
$ tar xvzf riscv-gnu-toolchain.tar.gz
$ make -j12
```

<br>

<개별 build 및 설명>

- linux build
```
$ ./build-linux.sh
or
$ cd work/linux
$ make clean
$ cd ../../
$ make -j12
linux build 시 work/sysroot 를 묶어 rootfs 로 만든뒤 vmlinux에 붙여준다. 이후 bbl.bin 으로 다시 생성된다.
```

- pk build - bootloader(bbl)
```
$ ./build-pk.sh
of
$ cd work/riscv-pk
$ make clean
$ cd ../../
$ make -j12
vmlinux 및 sysroot를 제외하고, riscv-pk 만 다시 빌드하여 bbl.bin 을 생성한다.
```

- buildroot build
```
$ build-buildroot.sh
of
$ rm -rf work/buildroot
$ rm -rf sysroot
$ cd buildroot
$ make clean
$ cd ../
$ make -j12
```
만약 sysroot에 임의로 파일을 추가/삭제가 필요한 경우(test를 위해서)에는 work/sysroot 에서 적절히 작업 후 linux build를 수행하면 된다.

<br>

### 3. running on target board
target board (FPGA board)에서 bbl.bin, BOOMConfig.cfg 을 load 하고 BOOM core 를 reset 할 수 있는 환경이 있어야한다.
target board를 는 ssh 로 연결하여 사용하는 것으로 가정한다.
target board setup(bit file burning) 참고 : RISC-V FPGA Test guide
(local) $ scp -r tools/target-tools/* {myServer}@192.168.1.xxx:/home/abc/anywhere/
(remote) $ cd /home/abc/anywhere/
(remote) $ ./run.sh
ID : root
PW : nexell

>>
NEXELL, INC.
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
x xxxxxx xx xx xxxxxxxxx xx xx xxxxxxx xxxxxxx
x xxxxx xx xx xxxxxxx xxx xx xxxxxxx xxxxxxx
x xxxx xx xxxxxxxx xxxxx xxxx xxxxxxx xxxxxxx xxxxxxx
x xxx xx xxxxxxxxx xxx xxxxx xxxxxxx xxxxxxx xxxxxxx
x xx xx xxxxxx x xxxxxx xxx xxxxxxx xxxxxxx
x x x xx xxxxxxx xxxxxxx xxx xxxxxxx xxxxxxx
x xx xx xxxxxxxxxx x xxxxxx xxxxxxx xxxxxxx xxxxxxx
x xxx xx xxxxxxxxx xxx xxxxx xxxxxxx xxxxxxx xxxxxxx
x xxxx xx xxx xxxxx xxxx xx xx x
x xxxxx xx xx xxxxxxx xxx xx xx x
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Nexell RISC-V
[ 0.000000] Linux version 4.6.2-g1bbe570-dirty (suker@swlab-server) (gcc version 6.1.0 (GCC) ) #8 Thu Nov 16 18:16:34 KST 2017
[ 0.000000] bootconsole [early0] enabled
[ 0.000000] Available physical memory: 246MB
[ 0.000000] Initial ramdisk at: 0xffffffff800199e8 (4100056 bytes)
[ 0.000000] Zone ranges:
[ 0.000000] Normal [mem 0x0000000080a00000-0x000000008fffffff]
[ 0.000000] Movable zone start for each node
[ 0.000000] Early memory node ranges
[ 0.000000] node 0: [mem 0x0000000080a00000-0x000000008fffffff]
[ 0.000000] Initmem setup node 0 [mem 0x0000000080a00000-0x000000008fffffff]
[ 0.000000] Built 1 zonelists in Zone order, mobility grouping on. Total pages: 62115
[ 0.000000] Kernel command line: earlyprintk 
[ 0.000000] PID hash table entries: 1024 (order: 1, 8192 bytes)
[ 0.000000] Dentry cache hash table entries: 32768 (order: 6, 262144 bytes)
[ 0.000000] Inode-cache hash table entries: 16384 (order: 5, 131072 bytes)
[ 0.000000] Sorting __ex_table...
[ 0.000000] Memory: 239760K/251904K available (3069K kernel code, 161K rwdata, 636K rodata, 4112K init, 231K bss, 12144K reserved, 0K cma-reserved)
[ 0.000000] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[ 0.000000] NR_IRQS:0 nr_irqs:0 0
[ 0.000000] clocksource: riscv_clocksource: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 1911260446275 ns
[ 0.000000] Calibrating delay loop (skipped), value calculated using timer frequency.. 2.00 BogoMIPS (lpj=10000)
[ 0.000000] pid_max: default: 32768 minimum: 301
[ 0.000000] Mount-cache hash table entries: 512 (order: 0, 4096 bytes)
[ 0.010000] Mountpoint-cache hash table entries: 512 (order: 0, 4096 bytes)
[ 0.020000] devtmpfs: initialized
[ 0.030000] clocksource: jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 19112604462750000 ns
[ 0.040000] NET: Registered protocol family 16
[ 0.050000] plic plic: enabling 2 IRQs
[ 0.090000] vgaarb: loaded
[ 0.090000] SCSI subsystem initialized
[ 0.090000] usbcore: registered new interface driver usbfs
[ 0.100000] usbcore: registered new interface driver hub
[ 0.100000] usbcore: registered new device driver usb
[ 0.100000] pps_core: LinuxPPS API ver. 1 registered
[ 0.110000] pps_core: Software ver. 5.3.6 - Copyright 2005-2007 Rodolfo Giometti <giometti@linux.it>
[ 0.110000] PTP clock support registered
[ 0.120000] clocksource: Switched to clocksource riscv_clocksource
[ 0.130000] NET: Registered protocol family 2
[ 0.140000] TCP established hash table entries: 2048 (order: 2, 16384 bytes)
[ 0.140000] TCP bind hash table entries: 2048 (order: 2, 16384 bytes)
[ 0.150000] TCP: Hash tables configured (established 2048 bind 2048)
[ 0.150000] UDP hash table entries: 256 (order: 1, 8192 bytes)
[ 0.150000] UDP-Lite hash table entries: 256 (order: 1, 8192 bytes)
[ 0.160000] NET: Registered protocol family 1
[ 2.870000] Unpacking initramfs...
[ 5.640000] console [sbi_console0] enabled
[ 5.640000] console [sbi_console0] enabled
[ 5.650000] bootconsole [early0] disabled
[ 5.650000] bootconsole [early0] disabled
[ 5.660000] futex hash table entries: 256 (order: 0, 6144 bytes)
[ 5.660000] workingset: timestamp_bits=61 max_order=16 bucket_order=0
[ 5.760000] io scheduler noop registered
[ 5.760000] io scheduler cfq registered (default)
[ 6.270000] e1000e: Intel(R) PRO/1000 Network Driver - 3.2.6-k
[ 6.270000] e1000e: Copyright(c) 1999 - 2015 Intel Corporation.
[ 6.270000] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
[ 6.280000] ehci-pci: EHCI PCI platform driver
[ 6.280000] usbcore: registered new interface driver usb-storage
[ 6.280000] usbcore: registered new interface driver usbhid
[ 6.290000] usbhid: USB HID core driver
[ 6.290000] NET: Registered protocol family 17
[ 6.340000] Freeing unused kernel memory: 4112K (ffffffff80000000 - ffffffff80404000)
[ 6.350000] This architecture does not have kernel memory protection.
[ 6.350000] [NEXELL] run_init_process = /init
Starting logging: OK
Starting mdev...
modprobe: can't change directory to '/lib/modules': No such file or directory
Initializing random number generator... [ 12.500000] random: dd urandom read with 0 bits of entropy available
done.
Starting network...
Waiting for interface eth0 to appear............... timeout!
run-parts: /etc/network/if-pre-up.d/wait_iface: exit status 1
Starting dropbear sshd: OK
Welcome to Buildroot
buildroot login: root
Password: 
#

<br>

### 4. buildroot customize
buildroot 를 customize 하고 싶을 때 두 가지 방법이 있다.
첫번째 방법은 work/sysroot 를 수정한뒤 linux build(위의 3. build guide참고) 를 실행하는 것이다. build time시 root filesyste을 다시 생성하여 vmlinux에 묶어준다.
두번째 방법은 buildroot 를 menu config을 사용하여 re-build 하는 것이다.
```
$ rm -rf work/buildroot
$ rm -rf work/sysroot
$ cd buildroot
$ make clean
$ make riscv64_defconfig
$ make menuconfig ==> 수정
$ cd ../
$ make -j12
```

<br>

### 5. working history
http://swjira.nexell.co.kr:8091/pages/viewpage.action?pageId=29196612
http://swjira.nexell.co.kr:8091/pages/viewpage.action?pageId=29982966
http://swjira.nexell.co.kr:8091/pages/viewpage.action?pageId=29984053
http://swjira.nexell.co.kr:8091/pages/viewpage.action?pageId=30310684
* a. BOOM spec 확인. 
* b. riscv-tools.git 소스를 이용하여 qemu 동작 확인
```
$ git clone https://github.com/riscv/riscv-tools.git
$ git submodule update --init --recursive
$ make CROSS_COMPILE=../riscv-gnu-toolchain/bin/riscv64-unknown-elf- ARCH=riscv vmlinux
$ export RISCV=/home/suker/RISC-V/riscv-gnu-toolchain/
==>? pk build ==> --with-payload=vmlinux
```
* c. yocto base 로 작업하는 것이 더 쉬움.
```
$ repo init -u ssh://{USER_ID}@git.nexell.co.kr:29418/nexell/riscv/manifest
$ repo sync
$ cd repoRISCV/yocto/riscv-poky
$ repoRISCV/yocto/riscv-poky$ source oe-init-build-env
$ repoRISCV/yocto/riscv-poky/build$ bitbake core-image-riscv
$ repoRISCV/yocto/riscv-poky/build$ runspike riscv64
```
* d. FPGA board based work - riscv-tools.git source base 로 작업.
```
$ git clone https://github.com/riscv/riscv-tools.git
$ cd $TOP/riscv-tools $ git submodule update --init --recursive
$ sudo apt-get install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc
$ export RISCV=$TOP/riscv
$ export PATH=$PATH:$RISCV/bin
$ ./build.sh
```
bbl (bootloader)에 debugging 용 assem code 를 삽입하여 debugging
* e. riscv-toolchain build시 CFLAG, LDFLAG 옵션에 따라 동작이 달라짐.
* f. hart (hardware thread) 에 대한 이해가 필요함.
* g. bbl 을 만들때 vmlinux 를 with-payload option 으로 붙여서 만드는 이유를 알 수 없어, 따로 떼어서 작업함. 
    bbl에서 vmlinux를 load elf 하여 동작시키도록 함. --> 실패.
* h. sifive source 를 reference 로 하여 재 작업함
    (sifive - freedom-u-sdk : https://www.sifive.com/documentation/freedom-soc/freedom-u500-vc707-fpga-dev-kit-getting-started-guide/)
* i. sifive 에서는 bbl+vmlinux+sysroot 를 하나로 붙여서 bbl.bin 을 만들어 사용함. base ver : linux 4.6.2
$ git clone --recursive https://github.com/sifive/freedom-u-sdk.git
$ cd freedom-u-sdk; make
* j. sbi_console tty driver가 내장되어있는것으로 보임. Nexell uartlite IP 와의 정합 작업이 필요함을 인지하는데까지 시간이 걸림.
* k. uart resigter 초기화 및 TX, RX validation check routine 을 추가하여 정상 부팅 확인함.

<br>

### 6. trouble shooting
* a. first build시 toolchain 을 생성하여야하기 때문에 오랜 시간이 걸린다. build 환경에 따라서 다르지만 약 1시간 가량 소요된다.
* b. riscv-tool-chain 은 버전에 따라서 linux build option 이 따라간다. build가 잘 안될시에는 build 시스템의 환경 변수에 RISCV 설정을 확인해본다.
