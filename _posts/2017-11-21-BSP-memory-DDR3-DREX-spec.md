---
title: 'DDR3 Samsung DREX-1 Spec-V2.0.3'
layout: post
tags:
  - BSP
category: BSP
---
#### DREX-1 V2.0.3 spec 정리, 분석

##Features

* PHY : is an abbreviation for the physical layer of the OSI model and refers to the circuitry required to implement physical layer functions.

Compatible JEDEC standard  LPDDR3/DDR3 SDRAMs
Supports 1:1 synchronous operation between AXI bus ACLK and scheduler CCLK
* ACLK : AXI Clock
* CCLK : DREX core clock
* AXI : Advanced eXtensible Interface, AMBA 3.0 Spec, AXI는 Burst기반으로 이루어져있다.
Write Response channel이 추가되어 있다. Read / Write 동시에 가능. 시작 주소만으로도 Burst Transfer가 가능하다.
고속 동작이 가능하도록 설계된 이 Bus는 ARM11이상의 Core를 사용하는 MCU의 backbone bus로 사용되고 있다.
* AMBA : Advanced Microcontroller Bus Architecture : Bus들을 어떻게 연결하고, IP끼리 서로 어떻게 통신 할 것이냐를 약속한 것.
-> SoC Target On-chip bus protocol, ARM에서 주도하는 Bus 규격. 
	* AMBA의 3가지 bus interface : AHB, ASB, APB
	* AHB : Advanced High-performance Bus
	* ASB : Advanced System Bus
	* APB : Advanced Peripheral Bus
![](http://pds15.egloos.com/pds/200906/07/90/c0098890_4a2b967986811.jpg)

Supports DFI 1:2 synchronous operation between ACLK/CCLK and memory clock domain.
* DFI : DDR PHY Interface, The DFI specification defines an interface protocol between memory controller logic and PHY interfaces

Integrated TrustZone address space control unit.

Parameterized number of slave ports (1, 2, 3 or 4) compatible with AMBA3 AXI protocol for accesses to SDRAM devices.

One slave port compatible with AMBA3 APB protocol for programmable special function registers.
Another slave port compatible with AMBA3 APB protocol for programmable special function registers of the
TrustZone address space control

Uses the DFI SDRAM PHY interface to support high-speed memory devices.
Supports up to two memory ranks (chip selects) and 4/8 banks per memory chips
Supports 1Gb, 2Gb, 4Gb and 8Gbit density per a chip select
Supports QoS scheme to ensure low latency for real-time applications
Out-of order scheduling policy for higher performance
Detects AXI RAR/WAW hazards automatically
Supports early write response
Supports rank/bank interleaving
Supports AMBA AXI low power interface for systemic power control
Adapts to various low power schemes to reduce the dynamic and static current of memory
Supports outstanding exclusive accesses
Supports bank selective precharge policy
Accommodates the embedded performance monitor
ca_swap signal for reversing ca[9:0] to ca[0:9] for LPDDR2/LPDDR3
Support Trust Zone Address Space Control