---
title: 'RISC-V qemu 정리2'
layout: post
tags:
  - RISC-V
category: Uncategoried
---
#### RISC-V QEMU2

<br>

### riscv-qemu device bring-up

QEMU device bring-up 방법론

	Challenges for driver developers:
		Real hardware is not available yet
		Hardware is expensive
		Hardware/software co-development
	How to develop & test drivers under these conditions?
		a. Implement device emulation in QEMU
		b. Develop driver against emulated device
		c. Verify against real hardware when available
	Plenty of examples in QEMU hw/ directory
		Learn from existing devices
		Documentation is sparse

<br>

### QEMU device bring-up

#### 추가
hw/riscv/nexell_v0_01.c
include/hw/riscv/nexell.h

#### build
앞 page 참고

#### expected result
```
$ ./riscv64-softmmu/qemu-system-riscv64 -machine \?
Supported machines are:
nexell_v1.10 RISC-V Nexell Board (Privileged ISA v1.10)
```

