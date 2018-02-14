---
layout: post
tags:
  - RISC-V
title: 'RISC-V ARM openocd + eclipse'
category: Uncategoried
---
#### RISC-V ARM + openocd + eclipse : my work history in Nexell

<br>

### openocd (JTAG debugger : olimex-arm-usb-ocd) + eclipse + gdb.


### 1. sifive u-500 VC707 FPGA board 로 확인.
```
User guide ==> https://www.sifive.com/documentation/freedom-soc/freedom-u500-vc707-fpga-dev-kit-getting-started-guide/

source download ==> git clone --recursive https://github.com/sifive/freedom-u-sdk.git

sudo apt-get update
sudo apt-get install bison flex libgmp-dev libmpfr-dev libmpc-dev libssl-dev
sudo apt-get install libftdi-dev libftdi1

cd freedom-u-sdk
make
```
==> 실패, FPGA board 의 jtag port는 FPGA board 자체 debugging용 인듯. u-500 user guide 처럼 risc-v debugging을 위해서는 debugging 용 module을 따로 붙여야 하는듯.

<br>

### 2. olimex-arm-usc-ocd 사용 검증. cortex-a9 개발 보드 사용.
openocd 설치, 환경 구성은 아래와 같다.
```
$ sudo apt-get install git libtool automake texinfo
$ git clone http://git.code.sf.net/p/openocd/code openocd-code
$ cd openocd
$ git tag -l
$ git checkout v0.10.0

$ ./bootstrap
$ ./configure --enable-ftdi --enable-ft2232_ftd2xx
$ make
$ sudo make install
```

<br>

### 3. openocd 실행
lpc_h2148.cfg로 그냥 돌려 봄. --> 당연히 동작 안함.
```
$ openocd -f tcl/interface/ftdi/olimex-arm-usb-ocd.cfg -f tcl/board/olimex_lpc_h2148.cfg 
Open On-Chip Debugger 0.10.0 (2018-01-18-14:23)
Licensed under GNU GPL v2
For bug reports, read
http://openocd.org/doc/doxygen/bugs.html
init_targets
Warning - assuming default core clock 12MHz! Flashing may fail if actual core clock is different.
trst_and_srst separate srst_gates_jtag trst_push_pull srst_open_drain connect_deassert_srst
adapter_nsrst_delay: 100
Info : auto-selecting first available session transport "jtag". To override use 'transport select <transport>'.
jtag_ntrst_delay: 100
adapter speed: 1500 kHz
Info : clock speed 1500 kHz
Info : JTAG tap: lpc2148.cpu tap/device found: 0x4ba00477 (mfg: 0x23b (ARM Ltd.), part: 0xba00, ver: 0x4)
Warn : JTAG tap: lpc2148.cpu UNEXPECTED: 0x4ba00477 (mfg: 0x23b (ARM Ltd.), part: 0xba00, ver: 0x4)
Error: JTAG tap: lpc2148.cpu expected 1 of 2: 0x3f0f0f0f (mfg: 0x787 (<unknown>), part: 0xf0f0, ver: 0x3)
Error: JTAG tap: lpc2148.cpu expected 2 of 2: 0x4f1f0f0f (mfg: 0x787 (<unknown>), part: 0xf1f0, ver: 0x4)
Error: Trying to use configured scan chain anyway...
Warn : Bypassing JTAG setup events due to errors
Info : Embedded ICE version 0
Error: unknown EmbeddedICE version (comms ctrl: 0x00000000)
Info : lpc2148.cpu: hardware has 2 breakpoint/watchpoint units
```

<br>

### 4. openocd .cfg file 작성
* cortex_a 와 cortex_m 은 target create 부분이 다르다. dbgbase address 를 사용중인 chipset에 맞게 설정해주어야 한다.
* tapid 의 경우 잘못된 id값을 넘기면 openocd에서 잘 맞춰달라고 정상값을 보여준다.

```
# board/nexell.cfg
source [find interface/ftdi/olimex-arm-usb-ocd.cfg]

source [find mem_helper.tcl]
source [find tools/memtest.tcl]

adapter_khz 10000

set CHIPNAME nxp4330
source [find target/nexellyyyy.cfg]

reset_config trst_and_srst
```

```
# target/nexellyyyy.cfg
if { [info exists CHIPNAME] } {
    set _CHIPNAME $CHIPNAME
} else {
    set _CHIPNAME nxp4330
}

if { [info exists DAP_TAPID] } {
    set _DAP_TAPID $DAP_TAPID
} else {
    set _DAP_TAPID 0x4ba00477
}

jtag newtap $_CHIPNAME dap -expected-id $_DAP_TAPID -irlen 4 -ircapture 0x01

set _TARGETNAME $_CHIPNAME.cpu

target create $_TARGETNAME cortex_a -chain-position $_CHIPNAME.dap -dbgbase 0x80110000 -rtos auto

$_TARGETNAME configure -event gdb-attach {
    cortex_a dbginit
}
```

<br>

### 5. terminal을 3개 띄운다.
```
$ telnet localhost 4444
```
```
$ arm-poky-linux-gnueabi-gdb bl1-navi.elf
...
(gdb) tartget remote localhost:3333
(gdb) monitor reset halt
(gdb) load
...
(gdb) directory /home/suker/bl1
(gdb) b main ##breakpoint main func
(gdb) continue
```
```
$ openocd -f nexell.cfg
```

<br>

### 6. tcp/3333 이 busy할 때
```
$ netstat -antu | grep 3333
tcp 0 0 127.0.0.1:3333 127.0.0.1:48058 FIN_WAIT2 
tcp 1 0 127.0.0.1:48058 127.0.0.1:3333 CLOSE_WAIT

$ fuser -k 3333/tcp

$ netstat -antu | grep 3333
tcp 1 0 127.0.0.1:48058 127.0.0.1:3333 CLOSE_WAIT
```

<br>

### 7. eclipse setting
![](/assets/ext_images/riscv/riscv-eclipse-setting1.png)
![](/assets/ext_images/riscv/riscv-eclipse-setting2.png)
![](/assets/ext_images/riscv/riscv-eclipse-setting3.png)
![](/assets/ext_images/riscv/riscv-eclipse-setting4.png)

<br>

### 8. RISC-V
risc-v 설정은 약간 다름
https://gnu-mcu-eclipse.github.io/debug/openocd/riscv/

<br>

### 9. To do
kernel debugging을 하려면 SMP, MMU 등등의 detail한 부분의 setting을 해주어야 한다.
risc-v는 bootloader까지만 완성하면되므로 일단 pass 한다.