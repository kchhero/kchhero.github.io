---
category: Uncategoried
layout: post
tags:
  - linux
title: Kernel(1)
---
summary : kernel 관련 기억/요약/link
### Kernel(1)

참고 : http://studyfoss.egloos.com/category/Kernel

---

#### typeof
GCC permits the identification of a type through the reference to a variable. This kind of operation permits a form of what's commonly referred to as generic programming. Similar functionality can be found in many modern programming languages such as C++, Ada, and the Java™ language. Linux uses typeof to build type-dependent operations such as min and max. Listing 1 shows how you can use typeof to build a generic macro (from ./linux/include/linux/kernel.h).
Listing 1. Using typeof to build a generic macro
```
#define min(x, y) ({                \
    typeof(x) _min1 = (x);          \
    typeof(y) _min2 = (y);          \
    (void) (&_min1 == &_min2);      \
    _min1 < _min2 ? _min1 : _min2; })
```

---

#### Clock Domain
| Clock Domain  | Description  |
| ------------ | ------------ |
| DCLK | Debugger clock synchronous to core  |
| FCLK  | WIC clock  |
| SCLK  | System Tick and NVIC clock  |
| HCLK  | AHB peripheral clock without keep awake feature  |
| GCLK  | AHB peripheral clock with keep awake feature  |
| PCLK  | APB peripheral clock with keep awake feature  |
| CLK  | Primary clock input to hardened subsystem  |
| PLLCLK  | Primary clock input to digital section of the via configurable ASIC  |

- FCLK : cpu에서 사용되는 클럭
- HCLK : AHB 버스에 사용되는 클럭(메모리 컨트롤러, 인터럽트 컨트롤러,  LCD 컨트롤러, DMA, USB 호스트 등에서 사용)
- PCLK : APB 버스에 사용되는 클럭(WDT, 타이머, ADC, UART, GPIO, RTC, SPI 등에서 사용)

---

#### initrd, ramdisk
참고 link : 
[http://egloos.zum.com/furmuwon/v/11145268](http://egloos.zum.com/furmuwon/v/11145268)

---
