---
title: 'development - GDB'
layout: post
tags:
  - development
category: private
---
#### GDB 사용법 정리

참고 : https://medium.com/scarabcandy/gdb%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EA%B8%B0%EB%B3%B8%EC%A0%81%EC%9D%B8-%EB%94%94%EB%B2%84%EA%B9%85-%EB%B0%A9%EB%B2%95-a488f07c20e0

<br>

### breakpoint
특정 line
```
(gdb) b iSDHCBOOT.c:1540
```
function 단위
```
(gdb) b iROMBOOT
```

<br>

### condition break
ex) ra register가 0x000000e40000003a 일 때 break 잡기
```
(gdb) watch $ra == 0x000000e40000003a
Watchpoint 4: $ra == 0x000000e40000003a
```

<br>

### register 값 확인
```
(gdb) x/4xw $ra

0x27ea <SDMMCBOOT+284>: 0x1101b7b9 0xe822ec06 0x842ae426 0x2b9000ef
```

<br>

### memory dump
```
(gdb) dump memory /home/suker/tmp/gdbdump 0x8fff0000 0x8fff7fff
```
