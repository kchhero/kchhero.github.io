---
categories: Uncategoried
category: BSP
layout: post
tags:
  - BSP
title: 'DDR3 knowledge memo'
---
#### memory 관련 자료 모음, 요약 분석

##### 1 clock 2 data  access => double data rate , DDR

##### [DQ group]
DQ        =>  DQ signal --> data bus (write : D, read : Q)  ,    DQ0~DQ7
DQS     =>  data bus strobe signal --> data clock signal
DQS +, DQS -   =>  difference signal line
DQM   => data bus mask

* DQS : DDR 메모리를 이루는 신호선.
* DQ 는 데이타버스를, DQS 는 데이타버스 Strobe 신호선을 의미. (데이타의 클럭신호선)
* DQS, DQM 신호선은 데이타버스 8비트당 하나씩.
* DQS 는 보통 DQS+, DQS- 두개의 신호선으로 이루어져 있으며  Difference 신호선이다. 이 두선은 각 선의 두께 만큼 떨어저 평행하게, 반드시 같이 움직여야 함. 두선의 길이는 거의 오차가 없이 일치해야 함.  (DQS 신호선은 때에 따라 DQS+ 신호선만 쓰일수도 있다.)
* DQS+ 와 DQS - 는 위상차가 180도 나며 이 두선을 스코프로 찍어보면 **EYE**  형상이 나오는데 이 형상이 뚜렸해야 한다. 

<br>

##### DLL : Delay-Locked Loop

##### RAS : Row access strobe
##### CAS : Column access strobe
##### CL : CAS Latency
##### EMRS : Extended Mode Register Set

##### MRS : 
##### MPR : 

<br>

#### ODT
DRAM controller가 독립적으로 termination 저항을 ON/OFF  시킴으로써 memory channel의 signal integrity를 향상.
* signal reflection : 전기적 신호가 전송선로를 따라서 진행하다가 선로의 끝에 부딪히면 신호가 반사되게 되며, 이는 noise가 되어서 선로의 신호품질을 떨어뜨리게 된다. reflection을 방지하기 위해 선로의 끝 부분에 적절한 값의 termination 저항을 사용하여 임피던스를 맞춰줘야 한다.
* termination resistance : DDR2 SDRAM 이 동작하는 아주 높은 주파수에서는 위와 같은 termination 저항을 사용하는것이 적합하지 않다. high frequency  systemd 에서 reflection signal에 대해 조금 더 정밀하게 제어할 필요가 생겼다. 이것이 ODT.
* ODT 는 DRAM 내부에 termination 저항을 넣어서 제어한다. 외부에 termination 저항을 붙이는것보다 패턴들의 수를 줄일 수 있어 패턴에 의한 영향이 적고, 부품의 숫자가 줄어들어 면적면에있어서 장점이 있다.
DQ,DQS,/DQS,RDQS,/RDQS pin에 대하여 ODT를 설정 할 수 있으며, ON/OFF도 제어할 수 있다.
EMRS register 를 통하여 설정 할 수 있다.

<br>

##### tREF : Refresh Time, 값이 작을수록 성능이 떨어진다
##### tRFC : Refresh Cycle Time, refresh 자체에 소요되는 시간을 규정한 것

##### JEDEC : Joint Electron Device Engineering Council

##### AHB : Advanced High-performance Bus, 단일 채널 버스, 공유 버스.
##### AXI : Advanced eXitensible Interface, 다중 채널 버스, 읽기/쓰기 최적화 버스.
##### AMBA : Advanced Microcontroller Bus Architecture
AHB 프로토콜의 경우는 address phase와 data phase가 함께 이어져 있어야 하기 때문에 DDR SDRAM 이나 플래쉬 메모리처럼 접근시 초기 latency가 있을 경우 data가 전송되지 않으면서도 버스를 점유할 수 밖에 없는 상황이 발생. 
반면, AXI 프로토콜의 경우는 address와 data channel을 독립적으로 분리하여 칩상의 네트워크를 쓸데없이 점유하는 상황을 피할 수 있다.

<br>

#### DDR2 T-Topology
* DDR2용으로 채택된 T-topology는 capacitive loading 문제로 인하여 higher signaling rates 및 더 많은 memory module을 지원하기 어려웠다.

![](https://m.eet.com/media/1190672/f1xl.jpg)
<i>Shows T-topology for connecting memory controller and DDR2 memory modules in which the Command/Address/Clock signals are routed to each memory module in a branched fashion.</i>I>

<br>

#### Fly-By Topology
* T-topology 의 문제를 해결하기 위하여 command 및 address  signals 를 각 메모리 module과 serialize 하게 연결하고 마지막에 적절한 종단을 연결하는 DDR3용 fly-by topology를 채택함으로써 극복하였다.
각각의 모듈에 signal이 다른 시간 간격으로 도달하게 되는데, 이것에 의해 도입된 개념이 write leveling 이다.

![](https://m.eet.com/media/1190673/f2xl.jpg)
<I>Depicts Fly-by topology for connecting memory controller and DDR3 memory modules in which the memory modules share common Command/Address/Clock lines connected in series.</I>

* DDR3 Module은 Fly-By Topology 때문에
Clock/Command/Address에 대한 DQ/DQS의 Timing을 각각의 DRAM마다 보정해주어야 한다

<br>

#### Write leveling
* memory device의 write 동작시 tDQS margin을 개선하기 위해서 data strobe signal(DQS)와 CLK 간의 왜곡(Skew)을 Calibration 하는 동작을 말한다
![](https://www.micron.com/datasheets/4gb_ddr4_dram_2e0d_micronxhtml/ddr4_write_leveling.png)

* write leveling은 EMRS(Extended Mode Register Set) 세팅에 의해서 Write Leveling Mode로 들어간 후 이루어지는데, DQS의 Rising Ddge에서 CLK의 상태를 DQ로 내보냄으로써 이루어진다. 즉, DQS의 rising edge에서 CLK의 상태가 high이면 WT_CTRL를 'high'로 내보내고, CLK의 상태가 low이면 WT_CTRL를 'low'로 내보낸다.
* memory controller 는 DQS를 CK 에 맞춰 조정하기 위해 write leveling 기능 및 feedback 을 사용한다.
DQS의 rising edge를 DRAM pin의 CLK edge와 맞추기위해 DQS에서 조절 가능한 delay setting을 갖는다.
DQ가 0 -> 1 로 변할 때 controller에서 DQS delay setting을 locking 한다.

#### Gate Leveling
The goal of gate training is to locate the delay at which the initial read DQS rising edge aligns with the rising edge of the read DQS gate(=ctrl_gate_p0/p1). Once this point is identified, the read DQS gate can be adjusted prior to the DQS, to the approximate midpoint of the read DQS preamble. You can use "GATE Leveling" when using "DDR3 memory" over 800MHz.

<I>Warning:
Don’t use Gate Leveling for productions in case of LPDDR2 or LPDDR3.</I>I>

#### Read DQ Calibration(=READ LEVELING, READ DESKEWING)
Read DQ Calibration adjusts for the delays introduced by the package, board and on-chip that impact the read cycle.

#### Write DQ Calibration(=WRITE DESKEWING)
Write DQ Calibration adjusts for the delays introduced by the package, board and on-chip that impact the write
cycle.


#### Precharge vs Refresh
* Precharge : refresh가 steady state일때의 전하 방전을 보상하기 위한 주기적인 충전 과정이라고 한다면, precharge는 데이저 read시 감쇄되는 전하(read operation, destructive read)를 보상하기 위하여 read 후 재충전하는 과정.
* Refresh : DRAM의 memory-cell 에서 capacitor에 전하가 채워져 있는 상황을 유지하고 있을때 이를 주기적으로 재충전 시키는 것.

<br>

#### DRAM cell operation, Read/Write
![](https://qph.ec.quoracdn.net/main-qimg-fc6c6b4d817d4fd7d9babcfe9eacc656.webp)
* For a a 1T DRAM cell, the data is stored as charge on the capacitor.
To write data, the bit-line (BL) is pulled-up(1) or pulled-down(0) to the value to be written. And WL (word-line) decides which cell will be written.
But, the read operation is destructive implying that there is a possibility of data getting corrupted. Hence, the read operation is always followed by a write-back.
Data =1: The bit-line is pre-charged to 'Vdd/2'. If the data stored is '1', then the voltage of the bit-line rises slightly (due to charge-sharing). The small change in voltage of BL is detected by the sense amplifiers that tell the processor that a '1' was stored. Now, the processor performs write operation to write back a '1'. 
Data =0: The bit-line is pre-charged to 'Vdd/2'. If the data stored is '0', then the voltage of the bit-line falls slightly. The small change in voltage of BL is detected by the sense amplifiers that tell the processor that a '0' was stored. Now, the processor performs write operation to write back a '0'. 
The sense amplifiers speed up the read operation; as the BL has a large capacitance, charge/discharge takes longer time. Also, without sense amplifiers if we were to try to determine the logic level of data stored, the final voltage value may not lie in any of the allowed regions for logic '0' or logic '1'. 

<br>

#### PTL : pass transistor logic
#### pass-transistor
A transistor used as a switch to pass logic levels between nodes of a circuit, instead of as a switch connected directly to a supply voltage.

* NMOS transistor
![](https://qph.ec.quoracdn.net/main-qimg-1867a0f149a48d23dea5dc526dfb1694.webp)

<br><br>


#### DDR 관련 펌 글

** 출처: http://udteam.tistory.com/808 [IYD - Everything Inside Your Device] **

SDRAM : Synchronized Dynamic Random Access Memory

Single Date Rate (SDR) SDRAM, Double Date Rate (DDR) SDRAM

1. 셀 (메모리 내 비트가 저장되는 최소단위) 클럭
2. 입출력 버퍼 클럭
3. 외견상 클럭 (유효클럭)

![](http://cfile21.uf.tistory.com/image/260B7445561240E322AC99)

cell 의 물리적인 기본 구성 단위인 capacitor 의 충/방전 시간 때문에 cell clock 은 100~200 Mhz 를 넘어서기 어렵다.
따라서 2,4,8 개의 cell을 병렬로 묶어 직렬 스트림을 생성하는 방법을 통해 메모리의 속도를 끌어올리게 되었다.
이것과 유사한 사례가 바로 RAID. 대역폭을 늘리기 시작.

![](http://cfile8.uf.tistory.com/image/221A3245561240E414CFBE)

![](http://cfile25.uf.tistory.com/image/24086F45561240E5252355)

DDR4는 메모리 내부적으로 '듀얼채널'이 구현된 DDR3이라 보아도 틀리지 않다

셀 차원에서 2n RAID 구성을 구상하게 되고, 일거에 메모리 대역폭이 두 배 가까이 늘게 되었는데 이는 곧 입출력 버퍼의 작동속도가 그에 비례해 늘어나야 했음을 의미.
셀과 똑같이 100~133MHz로 작동하던 입출력 버퍼의 작동속도가 단숨에 200~266MHz로 뛰어올라야 했다는 것. 하지만 그렇게까지 급격히 클럭을 올릴 수 없는 물리적인 한계가 있었고 이에 대응방안으로 모색된 것이 입출력 버퍼의 클럭을 유지하되, 클럭 사이클의 라이징 엣지 (0->1) 와 폴링 엣지 (1->0) 양쪽 모두에 데이터를 전송하도록 하는 아이디어였다. 이것이 Double Date Rate (DDR) 의 기원.


Command Rate : 메모리 컨트롤러가 발행한 명령이 메모리에 접수되기까지의 소요시간. 표기되는 값의 단위는 작동속도(클럭)의 한 사이클입니다. 즉 CR2로 정의되었다면 두 클럭사이클이 경과한 후 메모리컨트롤러의 명령이 메모리에 가 닿는다는 뜻. 사이클 단위를 실제 시간 단위(초)로 환산하려면 사이클 값을 작동속도로 나눠 주어야 한다. 예)CR2인 메모리가 1000MHz의 유효클럭으로 작동한다면 실제 소요되는 시간은 2(사이클) ÷ 1,000,000,000(Hz) = 0.000000002초 = 2ns

Row Address Strobe Time(tRAS) : 셀이 액세스되는 동안 그 셀이 속한 행은 반드시 액티브 상태로 유지되어야 하는데, 이를 위해 (액세스 전) 미리 Precharge 된 행이 액티브 상태를 유지해야만 하는 최소한의 시간을 규정한 것. 다시 말해 Precharge와 Precharge 사이의 주기를 규정한 값, 이 시간 이내에 셀 액세스가 끝날 수 있도록 나머지 램타이밍 값이 설정되어 있어야 한다. 따라서 보통 CL + tRCD + tRP의 합보다 넉넉히 크게 주어지고, CL + tRCD + tRP와 조화를 이룰 경우 성능에 미치는 영향이 드러나지 않으나 지나치게 크면 성능이 떨어진다. CR과 마찬가지로 사이클 단위로 매겨집니다.

RAS Precharge Time(tRP) : tRAS 값을 주기로 하여 행 Precharge ("Row Adress Strobe Precharge")  수행 소요시간을 의미.

Row Address to Column Address Delay Time(tRCD) : 메모리컨트롤러는 셀을 액세스하기 위해 행 주소를 찾고, 그 후 열 주소를 찾는다. 여기에 소요되는 딜레이를 의미.

Column Access Strobe Latency(CAS Latency, CL) : 한 행 내에서 첫 열부터 마지막 열까지를 액세스하는 데 소요되는 총 시간을 의미. 사이클 단위로 표기.
CR과 함께 성능에 영향을 크게 미치는 항목 중 하나.
![](http://cfile28.uf.tistory.com/image/2372063956124CF3152DBC)
![](http://cfile22.uf.tistory.com/image/2415393656124C7803C4A9)

![](http://cfile23.uf.tistory.com/image/2579973856124EEB31435E)
전체 작동 시나리오를 통틀어 보면 CL이 등장하는 횟수가 가장 많다. 버스트 모드에서마저 CL은 꼬박꼬박 사용되기 때문. 
반면 tRCD는 한 행 안에서 액세스될 때에는 생략 가능하고, tRP는 행이 다르더라도 페이지가 같으면 재차 생략 가능. 따라서 CL에 의한 영향이 가장 크고, tRCD와 tRP가 차례로 그 뒤를 이음. tRAS는 직접 성능에 관여하지는 않는다. 따라서 메모리 스펙표에 CL-tRCD-tRP-tRAS 순으로 표기되는 것은 성능에 가장 영향을 많이 주는 것 순으로 나열한 합리적인 표기 순서다.

