---
title: 'DDR3 knowledge memo'
layout: post
tags:
  - BSP
category: BSP
---
#### memory 관련 자료 모음, 요약 분석

1 clock 2 data  access => double data rate , DDR

[DQ group]
    DQ        =>  DQ signal --> data bus (write : D, read : Q)  ,    DQ0~DQ7
    DQS     =>  data bus strobe signal --> data clock signal
        DQS +, DQS -   =>  difference signal line

    DQM   => data bus mask

DLL : Delay-Locked Loop

RAS : Row access strobe
CAS : Column access strobe
CL : CAS Latency

ODT : DRAM controller가 독립적으로 termination 저항을 ON/OFF  시킴으로써 memory channel의
             signal integrity를 향상.

tREF : Refresh Time, 값이 작을수록 성능이 떨어진다
tRFC : Refresh Cycle Time, refresh 자체에 소요되는 시간을 규정한 것

JEDEC : Joint Electron Device Engineering Council

AHB : Advanced High-performance Bus, 단일 채널 버스, 공유 버스.
AXI : Advanced eXitensible Interface, 다중 채널 버스, 읽기/쓰기 최적화 버스.
AMBA : Advanced Microcontroller Bus Architecture
AHB 프로토콜의 경우는 address phase와 data phase가 함께 이어져 있어야 하기 때문에 DDR SDRAM 이나 플래쉬 메모리처럼 접근시 초기 latency가 있을 경우 data가 전송되지 않으면서도 버스를 점유할 수 밖에 없는 상황이 발생. 
반면, AXI 프로토콜의 경우는 address와 data channel을 독립적으로 분리하여 칩상의 네트워크를 쓸데없이 점유하는 상황을 피할 수 있다.

<br><br>


출처: http://udteam.tistory.com/808 [IYD - Everything Inside Your Device]

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
