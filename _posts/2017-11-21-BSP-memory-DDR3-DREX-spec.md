---
title: 'DDR3 Samsung DREX-1 Spec-V2.0.3'
layout: post
tags:
  - BSP
category: BSP
---
#### DREX-1 V2.0.3 spec 정리, 분석

## Features

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
* QoS : Quality of Service

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

<br>

## Initialization
An initialization procedure consists of three procedures such as *PHY DLL initialization*, *setting controller register* and *memory initialization*.

#### DDR3 with PHY V6
1. Apply power. RESET# pin of memory needs to be maintained for minimum 200us with stable power. CKE is
pulled “Low” anytime before RESET# being de-asserted (min. time 10ns)
2. Set the PHY for DDR3 operation mode, RL/WL/BL register and proceed ZQ calibration. Refer to
"INITIALIZATION" in PHY manual.
3. Assert the ConControl.dfi_init_start field to high but leave as default value for other fields.(aref_en and
io_pd_con should be off.) Clock gating in CGControl should be disabled in initialization and training se-
quence.
4. Wait for the PhyStatus0.dfi_init_complete field to change to „1‟.
5. Deassert the ConControl.dfi_init_start field to low.
6. Set the PHY for dqs pulldown mode. (Refer to PHY manual)
7. Set the PhyControl0.fp_resync bit-field to „1‟ to update DLL information.
8. Set the PhyControl0.fp_resync bit-field to „0‟.
9. Set the MemBaseConfig0 register and MemBaseConfig1 register if needed.
10. Set the MemConfig0 register and MemConfig1 if needed..
11. Set the PrechConfig and PwrdnConfig registers.
12. Set the TimingAref, TimingRow, TimingData and TimingPower registers according to memory AC parame-
ters.
13. If QoS scheme is required, set the QosControl0~15 and QosConfig0~15 registers.
14. Confirm that after RESET# is de-asserted, 500 us have passed before CKE becomes active.
15. Confirm that clocks(CK, CK#) need to be started and stabilized for at least 10 ns or 5 tCK (which is larger)
before CKE goes active.
16. Issue a NOP command using the DirectCmd register to assert and to hold CKE to a logic high level.
17. Wait for tXPR(max(5nCK,tRFC(min)+10ns)) or set tXP to tXPR value before step 17. If the system set tXP to
tXPR, then the system must set tXP to proper value before normal memory operation.
18. Issue an EMRS2 command using the DirectCmd register to program the operating parameters. Dynamic
ODT should be disabled. A10 and A9 should be low.
19. Issue an EMRS3 command using the DirectCmd register to program the operating parameters.
20. Issue an EMRS command using the DirectCmd register to enable the memory DLL.
21. Issue a MRS command using the DirectCmd register to reset the memory DLL.
22. Issues a MRS command using the DirectCmd register to program the operating parameters without resetting
the memory DLL.
23. Issues a ZQINIT commands using the DirectCmd register.
24. If there are more external memory chips, perform steps 17 ~ 24 procedures for other memory device.
25. If any leveling/training is needed, enable ctrl_atgate, p0_cmd_en, InitDeskewEn and byte_rdlvl_en. Disable
ctrl_dll_on and set ctrl_force value. (Refer to PHY manual)
26. If write leveling is not needed, skip this procedure. If write leveling is needed, set DDR3 into write leveling
mode using MRS direct command, set ODT pin high and tWLO using WRLVL_CONFIG0 regis-
ter(offset=0x120) and set the related PHY SFR fields through PHY APB I/F(Refer to PHY manual). To gener-
ate 1 cycle pulse of dfi_wrdata_en_p0, write 0x1 to WRLVL_CONFIG1 register(Offset addr=0x124). To read
the value of memory data, use CTRL_IO_RDATA(offset = 0x150). If write leveling is finished, disable write
leveling mode in PHY register and set ODT pin low and disable write leveling mode of DDR3.
27. If gate leveling is not needed, skip 27 ~ 28. If gate leveling is needed, set DDR3 into MPR mode using MRS
direct command and set the related PHY SFR fields through PHY APB I/F. Do the gate leveling. (Refer to
PHY manual)
28. If gate leveling is finished, set DDR3 into normal operation mode using MRS command and disable DQS pull-
down mode.(Refer to PHY manual)
29. If read leveling is not needed skip 29 ~ 30. If read leveling is needed, set DDR3 into MPR mode using MRS
direct command and set proper value to PHY control register. Do the read leveling..(Refer to PHY manual)
30. If read leveling is finished, set DDR3 into normal operation mode using MRS direct command.
31. If write training is not needed, skip 31. If write training is nedded, set the related PHY SFR fields through PHY
APB I/F..(Refer to PHY manual). To issue ACT command, enable and disable WrtraCon-
fig.write_training_en . Refer to this register definition for row and bank address. Do write training. (Refer to
PHY manual)
32. After all leveling/training are completed, enable ctrl_dll_on. (Refer to PHY manual)
33. Set the PhyControl0.fp_resync bit-field to „1‟ to update DLL information.
34. Set the PhyControl0.fp_resync bit-field to „0‟.
35. Disable PHY gating control through PHY APB I/F if necessary(ctrl_atgate, refer to PHY manual).
36. Issue PALL to all chips using direct command. This is an important step if write training has been done.
37. Set the MemControl and PhyControl0 register.
38. Set the ConControl register. aref_en should be turn on.
39. Set the CGControl register for clock gating enable.

<br>

## Address Mapping
There are two ways to map the AXI offset address as shown below: 1) simple interleaved mapping 2) split column
interleaved mapping 3) randomized interleaved mapping 4) chip interleaved mapping

<br>

## Low Power Operation
The controller executes a low power memory operation in five ways as described below. Each feature is indepen-
dent of each other and executed at the same time.
When memory is in the SREF state, then the controller issue SRX command automatically if AXI request is com-
ming.
* SREF : self refresh
* SRE : self refresh enter
* SRX : self refresh exit

##### AXI low-power interface
##### Dynamic power down
##### Dynamic self refresh
##### Clock stop
##### Direct command

<br>

## Precharge Policy
There are two options for DREX-1 regarding precharge policy – port-selective precharge and timeout precharge
per port.

##### Port Selective Precharge
##### Timeout Precharge

<br>

## Quality of Service
DREX provides Quality of Service (QoS) feature to ensure low latency for real-time masters. Specifically, DREX
uses timeout based QoS enforcement scheme.

<br>

## Performance Profiling
DREX provides performance monitoring capability based on event counters accessible through APB interface.
Relevant registers are located on 0xE000 ~ 0xE140. Note that do not access to these addres before
PPCCLKCON.perev_clk_en is set to 1.
DREX has events counters which is called PPC. lists performance events in each domain.

Table 1-1 List of Performance Events
| Event Items | Clock Domain |
| -------- | -------- |
| 1. Total read requests counts | aclk |
| 2.Total read requests counts per bank | aclk |
| 3. Total write requests counts | aclk |
| 4.Total write requests counts per bank | aclk |
| 6. Cas command scheduled counts | cclk |
| 7. Page hit counts | cclk |
| 8.DRAM data channel cycle used | cclk |

<br>

## Trust Zone Address Space Control (TZASC)
TZASC performs security checks on AXI accesses to memory. This supports configurable number of regions.
Each region is programmable for size, base address, enable, and security parameters. Using the 
secure_boot_lock input signal, the programmers view can be locked to prevent erroneous writes. It provides 
programmability in reporting faults using AXI response channel, and interrupt.

#### Regions
A region is a contiguous area of address space. The TZASC provides each region with a programmable security
permissions field. The security permissions value is used to enable the TZASC to either accept or deny a transac-
tion access to that region. The transactions arprots[2:0] or awprots[2:0] signals are used to determine the security
settings of that transaction. The region features are as following:
* Minimum region granularity: 64kB
* Number of regions: 8 non-overlapping regions (region 1~8) + 1 default region (region 0)
* Priority of regions: priority_of_region_1-8 > priority_of_region_0
* Address space of a region:
-- Base address: aligned to 64kB
-- Size of a region: 64kB x N
-- Maximum size of a region: 4GB
-- Region 0 covers entire address space
* No support of subregions

The feature of security property of regions are as following:
* 4 bits permission
-- Each bit represent if (secure read, secure write, non-secure read, non-secure write) are allowed or not
-- EX) A region with security property b‟1100 allows secure access (read and write) but forbids non-secure
access
* 1 bit region_lock
-- Region configuration (SFR) is locked after secure_boot_lock is asserted
* 1bit region enable
-- Region 1~8 is enabled by SFR
-- Region 0 is always enabled.

<br>

## Clock Gating
DREX has clock gating feature for internal logic and PHY clock.
* Internal logic clock gating enable: int_cg_en in ConControl register
* PHY clock gating enable: phy_cg_en in ConControl register

<Br>

## Register Descriptions
##### Controller Control Register (ConControl, R/W, Address Offset=0x0000)
##### Memory Control Register (MemControl, R/W, Address Offset=0x0004)
##### Clock Gating Control Register (CGControl, R/W, Address Offset=0x0008)
This register should not be written to enable after all initialization and trainings have been finished.
##### Memory Direct Command Register (DirectCmd, R/W, Address Offset=0x0010)
##### Precharge Policy Configuration0 Register (PrechConfig0, R/W, Address Offset=0x14)
##### PHY Control0 Register (PhyControl0, R/W, Address Offset=0x0018)
##### Precharge Policy Configuration1 Register (PrechConfig1, R/W, Address Offset=0x1C)
##### AC Timing Register for Per Bank Refresh of Memory (TimingRFCpb, R/W, Address Offset=0x0020)
##### Dynamic Power Down Configuration Register (PwrdnConfig, R/W, Address Offset=0x0028)
##### AC Timing Register for Periodic ZQ(ZQCS) of Memory (TimingPZQ, R/W, Address Offset=0x002C)
##### AC Timing Register for Auto Refresh of Memory (TimingAref, R/W, Address Offset=0x0030)
##### AC Timing Register n for the Row of Memory (TimingRow n, R/W, Address Offset=0x0034(for n =
0), 0x00E4(for n = 1))
##### AC Timing Register n for the Data of Memory (TimingData n, R/W, Address Offset=0x0038(for n =
0), 0x00E8(for n = 1))
##### AC Timing Register n for the Power modes of Memory (TimingPower n, R/W, Address
Offset=0x003C(for n = 0), 0x00EC(for n = 1))
##### PHY Status Register (PhyStatus, Read Only, Address Offset=0x0040)
##### ETCTiming Register (ETCTIMING, R/W, Address Offset=0x0044)
##### Memory ChipStatus Register (ChipStatus, Read Only, Address Offset=0x0048)
##### Memory Mode Registers Status Register (MrStatus, Read Only, Address Offset=0x0054)
##### Quality of Service Control Register n (QosControl n, R/W, Address Offset=0x0060 + 8n (n=0~15,
integer))
##### Timing Set Switch Configuration Register(TimingSetSw, R/W Address Offset=0x00E0)
##### Write Training Configuration Register (WrTraConfig, R/W, Address Offset=0x00F4)
##### Read Leveling Configuration Register (RdlvlConfig, R/W, Address Offset=0x00F8)
##### BRB Reservation Control Register (BRBRSVCONTROL, R/W, Address Offset=0x0100)
##### BRB Reservation Configuation Register (BRBRSVCONFIG, R/W, Address Offset=0x0104)
##### BRB QoS Configuation Register (BRBQOSCONFIG, R/W, Address Offset=0x0108)
##### Write Leveling Configuration Register0 (WRLVLCONFIG0, R/W, Address Offset=0x0120)
##### Write Leveling Configuration Register1 (WRLVLCONFIG1, R/W, Address Offset=0x0124)
##### Write Leveling Status Register (WRLVLSTATUS, R/W, Address Offset=0x0128)
##### PPC Clock Control Register (PPCCLKCON, R/W, Address Offset=0x0130)
##### Performance Event Configuration0 Register (PerevConfig0, R/W, Address Offset=0x0134)
##### Performance Event Configuration1 Register (PerevConfig1, R/W, Address Offset=0x0138)
##### Performance Event Configuration2 Register (Perev2Config, R/W, Address Offset=0x013C)
##### Performance Event3 Configuration Register (PerevConfig3, R/W, Address Offset=0x0140)
##### CTRL_IO_RDATA Register (CTRL_IO_RDATA, R, Address Offset=0x0150)
##### CA Calibration Configuration Register0 (CACAL_CONFIG0, R/W, Address Offset=0x0160)
##### CA Calibration Configuration Register (CACAL_CONFIG1, R/W, Address Offset=0x0164)
##### CA Calibration Status Register (CACAL_STATUS, R, Address Offset=0x0168)
##### Emergent Configuration Register 0 (EMERGENT_CONFIG0, R/W, Address Offset=0x0200)
##### Emergent Configuration Register 1 (EMERGENT_CONFIG1, R/W, Address Offset=0x0204)
##### Back Pressure Control Register For Port n (BP_CONTROLn, R/W, Address Offset=0x0210 + 0x10n
(n=0~3))
##### Back Pressure Configuration Register For Read/Port n (BP_CONFIGn_R, R/W, Address
Offset=0x0214 + 0x10n(n=0~3))
##### Back Pressure Configuration Register For Write/Port n (BP_CONFIGn_W, R/W, Address
Offset=0x0218 + 0x10n(n=0~3))
##### Window Configuration for Write ODT Register (WinConfig_W_ODT, R/W, Address Offset=0x0300)
##### Window Configuration for CTRLREAD Register (WinConfig_CTRLREAD, R/W, Address
Offset=0x0308)
##### Window Configuration for CTRLGATE Register (WinConfig_CTRLGATE, R/W, Address
Offset=0x030C)
##### Performance Monitor Control Register (PMNC_PPC, R/W, Address Offset=0xE000)
##### Count Enable Set Register (CNTENS_PPC, R/W, Address Offset=0xE010)
##### Count Enable Clear Register (CNTENC_PPC, R/W, Address Offset=0xE020)
##### Interrupt Enable Set Register (INTENS_PPC, R/W, Address Offset=0xE030)
##### Interrupt Enable Clear Register (INTENC_PPC, R/W, Address Offset=0xE040)
##### Overflow Flag Status Register (FLAG_PPC, R/W, Address Offset=0xE050)
##### Cycle Count Register (CCNT_PPC, R/W, Address Offset=0xE100)
##### Performance Monitor Count0 Register (PMCNT0_PPC, R/W, Address Offset=0xE110)
##### Performance Monitor Count1 Register (PMCNT1_PPC, R/W, Address Offset=0xE120)
##### Performance Monitor Count2 Register (PMCNT2_PPC, R/W, Address Offset=0xE130)
##### Performance Monitor Count3 Register (PMCNT3_PPC, R/W, Address Offset=0xE140)

<br>

## TZASC Register Description

##### TZASC Configuration Register (TZCONFIG, R/O, Address Offset=0x0000)
##### TZASC Action Register (TZACTION, R/W, Address Offset=0x0004)
##### TZASC Lockdown Range Register (TZLDRANGE, R/W, Address Offset=0x0008)
##### TZASC Lockdown Select Register (TZLDSELECT, R/W, Address Offset=0x000C)
##### TZASC Interrupt Status Register (TZINTSTATUS, R/O, Address Offset=0x0010)
##### TZASC Interrupt Clear Register (TZINTCLEAR, W/O, Address Offset=0x0014)
##### TZASC Read Fail Address Low Register n (TZFAILADDRLOWRn, R/O, Address Offset=0x0040 +
0x20n (n=0~3, integer))
##### TZASC Read Fail Address High Register n (TZFAILADDRHIGHRn, R/O, Address Offset=0x0044 +
0x20n (n=0~3, integer))
##### TZASC Read Fail Control Register n (TZFAILCTRLRn, R/O, Address Offset=0x0048 + 0x20n (n=0~3,
integer))
##### TZASC Read Fail ID Register n (TZFAILIDRn, R/O, Address Offset=0x004C + 0x20n (n=0~3,
integer))
##### TZASC Write Fail Address Low Register n (TZFAILADDRLOWWn, R/O, Address Offset=0x0050 +
0x20n (n=0~3, integer))
##### TZASC Write Fail Address High Register n (TZFAILADDRHIGHWn, R/O, Address Offset=0x0054 +
0x20n (n=0~3, integer))
##### TZASC Write Fail Control Register n (TZFAILCTRLWn, R/O, Address Offset=0x0058 + 0x20n
(n=0~3, integer))
##### TZASC Write Fail ID Register n (TZFAILIDWn, R/O, Address Offset=0x005C + 0x20n (n=0~3,
integer))
##### TZASC Region Setup Low Register n (TZRSLOWn, R/W, Address Offset=0x0100 + 0x10n (n=0~8,
integer))
##### TZASC Region Setup High Register n (TZRSHIGHn, R/W, Address Offset=0x0104 + 0x10n (n=0~8,
integer))
##### TZASC Region Attribute Register n (TZRSATTRn, R/W, Address Offset=0x0108 + 0x10n (n=0~8,
integer))
##### TZASC Integration Test Control Register (TZITCRG, R/W, Address Offset=0x0E00)
##### TZASC Integration Test Input Register (TZITIP, R/O, Address Offset=0x0E04)
##### TZASC Integration Test Output Register (TZITOP, R/W, Address Offset=0x0E08)
##### Memory Chip0 Base Configuration Register (MemBaseConfig0, R/W, Address Offset=0x0F00)
##### Memory Chip1 Base Configuration Register (MemBaseConfig1, R/W, Address Offset=0x0F04)
##### Memory Chip0 Configuration Register (MemConfig0, R/W, Address Offset=0x0F10)
##### Memory Chip1 Configuration Register (MemConfig1, R/W, Address Offset=0x0F14)
