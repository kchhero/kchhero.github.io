---
title: 'DDR3 SDRAM Timing Diagram - samsung'
layout: post
tags:
  - BSP
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
