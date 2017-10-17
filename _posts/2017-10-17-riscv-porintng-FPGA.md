---
title: 'RISC-V porting on FPGA'
layout: post
tags:
  - RISC-V
category: Uncategoried
---
### Source Download

* boom spec
file:///home/suker/Downloads/boom-spec.pdf

* risc-v linux

```
$ git clone https://github.com/riscv/riscv-linux.git
```

* riscv-tool repository download & build

```
$ git clone https://github.com/riscv/riscv-tools.git
$ git submodule update --init --recursive
$ cd $TOP/riscv-tools
$ git submodule update --init --recursive
$ sudo apt-get install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc
$ export RISCV=$TOP/riscv
$ export PATH=$PATH:$RISCV/bin
$ ./build.sh
```

* bbl download & build

riscv-pk 의 bbl 로 작업이 어려워 lowriscv github 를 이용함.
```
$ git clone --recursive https://github.com/lowRISC/lowrisc-fpga
$ cd bootloader
$ mkdir build
$ cd build
$ export RISCV=/home/suker/RISC-V/riscv-suker
$ export PATH=$PATH:$RISCV/bin
$ ../configure --prefix=$RISCV --host=riscv64-unknown-elf --with-payload=../../../riscv-linux/vmlinux --enable-logo
$ make clean
$ make
$ riscv64-unknown-elf-objcopy -O binary bbl bbl.bin
$ scp -r bbl.bin fpga@192.168.1.182:/home/fpga/test/BOOM/uartlite/suker/
```
