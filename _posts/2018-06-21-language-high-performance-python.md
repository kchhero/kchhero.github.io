---
title: 'Python> High Performance Python(1)'
layout: post
tags:
  - Language
category: programming
---
### ch1 preview

#### GIL (Global Interpreter Lock)
multi thread 프로그래밍은 그다지 효율적이지 않지만 I/O bound되는 프로그램의 경우는 효과를 볼 수 있다.
multi processing 을 사용하여 성능 향상을 볼 수 있다.

<br>

### ch2 bottleneck

#### profling 기법
IPython의 %timeit, time.time(), timing decorator, cProfile, line_profiler, heapy, dowser, memory_profiler

https://github.com/kchhero/suker_python_project/tree/master/highperfbook/test2_1.py

unix의 time 명령어를 사용하는 방법
```
$ /usr/bin/time -p python test2_1.py
Length of x: 1000
Total elements: 1000000
calculate_z_serial_purepythontook 5.960104942321777 seconds
real 6.51
user 6.47
sys 0.04
```
more detail : --verbose 사용
* Major (requiring I/O) page faults : OS RAM에서 필요한 데이터를 찾을 수 없어서 disk에서 page를 불러 왔는지의 여부를 나타낸다.

<br>

#### 병목지점 파악방법
#### 프로파일링 방법
#### CPython 내부 동작, bytecode의 이해