---
title: 'development - qemu memo'
layout: post
tags:
  - development
category: private
---
#### QEMU study memo For RISC-V

* Dynamic Binary Translation을 사용하는 프로세서 emulator 이면서 hypervisor 이다.
* Fabrice Bellard 가 개발하였음. FFmpeg 을 개발한 사람.
* Dynamic Binary Translation은 실행파일의 코드를 target 아키텍처에서 실행되는 코드로 block 단위만큼만 순차적으로 읽어 변환한다.
* Host와 Guest의 CPU architecture가 다르거나 Guest의 CPU 속도가 host보다 느릴 경우 효과가 있다.

* bare metal vs hosted
![](/assets/ext_images/development/qemu1.png)

* HTIF (Host Target Interface) :  QEMU를 위한 console emulation 을 support 한다.
* HTIF를 사용하여 BBL과 Linux의 동일한 복사본을 Spike와 QEMU에서 실행할 수 있다.
* BBL은 SBI (Supervisor Binary Interface) 및 Linux kernel SBI console을 통해 HTIF console access를 제공한다.

* IP modeling 시 참고, sifive의 uart 구현부 ==> riscv-qemu/hw/riscv/sifive_uart.c
* power, reset, interrupt, clock ==> riscv-qemu/hw/riscv/sifive_prci.c
* QSPI 는 riscv-qemu/hw/riscv/sifive_e300.c 를 참고