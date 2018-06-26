---
layout: post
tags:
  - Language
title: 'Python> High Performance Python(1)'
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

* unix의 time 명령어를 사용하는 방법
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

* 프로파일링하기 전에 가설을 먼저 검증하는 습관을 들이는것이 좋다. 감으로 프로파일링하는 습관은 피하도록 한다.

* cProfile
```
$ python -m cProfile -s cumulative test2_1.py 
('Length of x:', 1000)
('Total elements:', 1000000)
('calculate_z_serial_purepythontook', 7.633919954299927, 'seconds')
         36221992 function calls in 8.216 seconds

   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.023    0.023    8.216    8.216 test2_1.py:1(<module>)
        1    0.480    0.480    8.193    8.193 test2_1.py:7(calc_pure_python)
        1    5.791    5.791    7.634    7.634 test2_1.py:40(calculate_z_serial_purepython)
 34219980    1.832    0.000    1.832    0.000 {abs}
  2002000    0.075    0.000    0.075    0.000 {method 'append' of 'list' objects}
        1    0.012    0.012    0.012    0.012 {range}
        1    0.004    0.004    0.004    0.004 {sum}
        2    0.000    0.000    0.000    0.000 {time.time}
        4    0.000    0.000    0.000    0.000 {len}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
```

<br>

* line_proofiler
Robert Kern의 line_profiler, 개별 함수를 한 줄씩 프로파일링할 수 있는 강력한 도구.

* https://github.com/rkern/line_profiler
```
$ pip install line_profiler
```

```
$ kernprof -l -v test2_1.py
Length of x: 1000
Total elements: 1000000
calculate_z_serial_purepythontook 70.00200009346008 seconds
Wrote profile results to test2_1.py.lprof
Timer unit: 1e-06 s

Total time: 39.5841 s
File: test2_1.py
Function: calculate_z_serial_purepython at line 40

Line #      Hits         Time  Per Hit   % Time  Line Contents
=============================================================
    40                                           @profile
    41                                           def calculate_z_serial_purepython(maxiter, zs, cs):
    42         1       1788.0   1788.0      0.0      output = [0] * len(zs)
    43   1000001     305261.0      0.3      0.8      for i in range(len(zs)):
    44   1000000     291756.0      0.3      0.7          n = 0
    45   1000000     335763.0      0.3      0.8          z = zs[i]
    46   1000000     315128.0      0.3      0.8          c = cs[i]
    47  34219980   15392219.0      0.4     38.9          while abs(z) < 2 and n < maxiter:
    48  33219980   12199147.0      0.4     30.8              z = z * z + c
    49  33219980   10407960.0      0.3     26.3              n += 1
    50   1000000     335111.0      0.3      0.8          output[i] = n
    51
    52         1          1.0      1.0      0.0      return output
```

<br>

* memory_profiler
$ pip install psutil
$ pip install memory_profiler
```
$ python3 -m memory_profiler test2_1.py
Length of x: 1000
Total elements: 100000
```

<br>

* 그외 heapy, dowser 등등

<br>

#### CPython 내부 동작, bytecode의 이해

* dis 모듈

```
#test2_dis_byte.py
import dis
import test2_1

dis.dis(test2_1.calculate_z_serial_purepython)

```
```
$ python test2_dis_byte.py
 41           0 LOAD_CONST               1 (0)
              3 BUILD_LIST               1
              6 LOAD_GLOBAL              0 (len)
              9 LOAD_FAST                1 (zs)
             12 CALL_FUNCTION            1
             15 BINARY_MULTIPLY     
             16 STORE_FAST               3 (output)

 42          19 SETUP_LOOP             123 (to 145)
             22 LOAD_GLOBAL              1 (range)
```

<br>

* 방식에 따른 복잡도

```
def fn_expressive(upper=100000):
    total = 0
    for n in range(upper):
        total += n
    return total

def fn_terse(upper=100000):
    return sum(range(upper))

print(fn_expressive())
print(fn_terse())
```
```
import dis
import test2_11

print("fn_expressive")
dis.dis(test2_11.fn_expressive)

print("fn_terse")
dis.dis(test2_11.fn_terse)
```
```
$ python test2_11_dis_byte.py 
4999950000
4999950000
fn_expressive
  2           0 LOAD_CONST               1 (0)
              3 STORE_FAST               1 (total)

  3           6 SETUP_LOOP              30 (to 39)
              9 LOAD_GLOBAL              0 (range)
             12 LOAD_FAST                0 (upper)
             15 CALL_FUNCTION            1
             18 GET_ITER            
        >>   19 FOR_ITER                16 (to 38)
             22 STORE_FAST               2 (n)

  4          25 LOAD_FAST                1 (total)
             28 LOAD_FAST                2 (n)
             31 INPLACE_ADD         
             32 STORE_FAST               1 (total)
             35 JUMP_ABSOLUTE           19
        >>   38 POP_BLOCK           

  5     >>   39 LOAD_FAST                1 (total)
             42 RETURN_VALUE        
fn_terse
  9           0 LOAD_GLOBAL              0 (sum)
              3 LOAD_GLOBAL              1 (range)
              6 LOAD_FAST                0 (upper)
              9 CALL_FUNCTION            1
             12 CALL_FUNCTION            1
             15 RETURN_VALUE
```

 내장 함수를 사용하는 경우가 더 간결하다. 그렇지 않은 경우는 루프가 반복될 때마다 변수 타입을 검사한다.