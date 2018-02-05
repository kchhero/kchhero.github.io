---
title: 'RISC-V qemu 정리'
layout: post
tags:
  - RISC-V
category: Uncategoried
---
#### RISC-V QEMU

<br>

### riscv-qemu
```
==> https://github.com/riscv/riscv-qemu
$ sudo apt-get install libpixman-1-dev
$ git clone https://github.com/riscv/riscv-qemu.git
$ make build
$ cd build
$ ../configure
($ ./configure --target-list=riscv64-softmmu,riscv32-softmmu)
(option없는 경우 full build 되는 듯)
$ make
qemu ==> ./riscv64-softmmu/qemu-system-riscv64
```

<br>

### riscv-tools
```
$ git clone https://github.com/riscv/riscv-tools.git
$ cd $TOP/riscv-tools
$ git submodule update --init --recursive
$ sudo apt-get install autoconf automake autotools-dev curl device-tree-compiler libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev
$ export RISCV=/home/suker/RISC-V/riscv-toolchain/
$ export PATH=$PATH:$RISCV/bin
$ ./build.sh
GDB ==> ./bin/riscv64-unknown-elf-gdb
```

<br>

### bbl, vmlinux
```
$ git clone --recursive https://github.com/sifive/freedom-u-sdk
$ cd freedom-u-sdk
==> riscv-pk/Makefile.in 수정
88 CFLAGS := @CFLAGS@ $(CFLAGS) -DBBL_PAYLOAD=\"bbl_payload\" -DBBL_LOGO_FILE=\"bbl_logo_file\" -g -ggdb
# -msoft-float 있으면 삭제
97 LDFLAGS := @LDFLAGS@ -nostartfiles -nostdlib -static $(LDFLAGS) -g -ggdb

$make -j8
```

<br>

### RUN
```
$ cd /home/suker/RISC-V/riscv-qemu/
$ ./riscv64-softmmu/qemu-system-riscv64 -S -s -kernel /home/suker/RISC-V/freedom-u-sdk/work/riscv-pk/bbl -nographic -m 2048M
```
```
$ cd /home/suker/RISC-V/riscv-toolchain/
$ ./bin/riscv64-unknown-elf-gdb /home/suker/RISC-V/sifive/test-u-500/work/riscv-pk/bbl
```

<br>

### gdb실행시 아래와 같은 message가 나오는 경우

```
Reading symbols from /home/suker/RISC-V/sifive/test-u-500/work/riscv-pk/bbl...(no debugging symbols found)...done.
```

riscv-pk/Makefile.in 에서 gdb flag를 enable해주어야한다.
```
88 CFLAGS := @CFLAGS@ $(CFLAGS) -DBBL_PAYLOAD=\"bbl_payload\" -DBBL_LOGO_FILE=\"bbl_logo_file\" -g -ggdb
89 BBL_PAYLOAD := @BBL_PAYLOAD@
90 COMPILE := $(CC) -MMD -MP $(CFLAGS) \
91 $(sprojs_include)
92 # Linker
93 # - LDFLAGS : Flags for the linker (eg. -L)
94 # - LIBS : Library flags (eg. -l)
95 
96 LD := $(CC)
97 LDFLAGS := @LDFLAGS@ -nostartfiles -nostdlib -static $(LDFLAGS) -g -ggdb
```

<br>

### etc.

아직 eclipse GDB qemu 는 사용할 수 없는듯. arm용 plugin 은 github에 있지만 risc-v 용은 없음.


[suker@suker-machine] ~/RISC-V/eclipse/qemu/2.8.0-201612271623-dev
$ ./bin/qemu-system-gnuarmeclipse -S -s -kernel /home/suker/RISC-V/sifive/test-u-500/work/riscv-pk/bbl
qemu-system-gnuarmeclipse: -kernel /home/suker/RISC-V/sifive/test-u-500/work/riscv-pk/bbl: Neither board nor mcu specified, and there is no default.
Use -board help or -mcu help to list supported boards or devices!
