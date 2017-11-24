---
layout: post
tags:
  - BSP
title: 'DDR3 SDRAM Timing Diagram - samsung'
category: Uncategoried
---
#### ddr3 device operation timing diagram 요약 분석

<br>

![](/assets/ext_images/BSP/DDR3/DDR3_SDRAM_TIMING_DIAGRAM_STATE_DIGRAM.png)
![](/assets/ext_images/BSP/DDR3/DDR3_SDRAM_TIMING_DIAGRAM_table1.png)

<br>

##### Additive Latency(AL)
* Additive Latency (AL) operation is supported to make command and data bus efficient for sustainable bandwidths in DDR3 SDRAM. In this operation, the DDR3 SDRAM allows a read or write command (either with or without auto-precharge) to be issued immediately after the active command. The command is held for the time of the Additive Latency (AL) before it is issued inside the device. The Read Latency (RL) is controlled by the sum of the AL and CAS Latency (CL) register settings.



#### ZQ Calibration :

<br>

#### Read Burst Operation

![](/assets/ext_images/BSP/DDR3/DDR3_Device_operation_timing_diagram_read_burst_operation1.png)

<br>

#### Read Timing Definitions
* Rising data strobe edge parameters:
-- tDQSCK min/max describes the allowed range for a rising data strobe edge relative to CK, CK.
-- tDQSCK is the actual position of a rising strobe edge relative to CK, CK.
-- tQSH describes the DQS, DQS differential output high time.
-- tDQSQ describes the latest valid transition of the associated DQ pins.
-- tQH describes the earliest invalid transition of the associated DQ pins.
* Falling data strobe edge parameters:
-- tQSL describes the DQS, DQS differential output low time.
-- tDQSQ describes the latest valid transition of the associated DQ pins.
-- tQH describes the earliest invalid transition of the associated DQ pins.
-- tDQSQ; both rising/falling edges of DQS, no tAC defined.

![](/assets/ext_images/BSP/DDR3/DDR3_Device_operation_timing_diagram_read_timing_definition.png)

<br>

#### Write Operation

![](/assets/ext_images/BSP/DDR3/DDR3_Device_operation_timing_diagram_write_operation.png)

![](/assets/ext_images/BSP/DDR3/DDR3_Device_operation_timing_diagram_write_burst_operation.png)

<br>

#### Refresh Command
* REF command는 지속성이 없으므로 매번 실행해야한다. 즉, 새로 고침이 필요하다.
tREFI interval 만큼의 시간 간격으로 refresh가 필요하다.
* CS, RAS,CAS가 low 이고 WE 가 rising edge high가 되면 refresh cycle이 시작된다.
* SDRAM의 모든 bank는 최소 tRP 동안 precharged 및 idle 상태여야한다.
* NOP 또는 DES를 제회한 다음 valid command 는 tRFC(min)보다 커야한다. tRFC는 memory density에 따라 다르다.
* delayed 되는 refresh의 총 수는 8개를 초과할 수 없다.
* tRFC : Refresh cycle time
 
![](/assets/ext_images/BSP/DDR3/DDR3_Device_operation_timing_diagram_refresh_command.png)


<br>

#### Self-Refresh Operation

![](/assets/ext_images/BSP/DDR3/DDR3_Device_operation_timing_diagram_selfrefresh_command.png)

<br>

#### ZQ Calibration

<br>

#### ODT

