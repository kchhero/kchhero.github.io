---
title: 'Python> High Performance Python(3)'
layout: post
tags:
  - Language
category: programming
---
summary : chapter5,6 내용 정리/요약, iterator / generator, matrix / vector

### ch5 iterator, generator
range는 주어진 범위의 모든 수를 담는 리스트를 생성하도록 구현되었다.
같은 횟수의 연산을 수행하더라도 xrange에 비해 10배 이상의 메모리를 사용한다.

xrange의 구현체
```
def xrange(start, stop, step=1):
    while start < stop:
        yield start
        start += step
```
값을 반환하는 대신 yield를 이용해 여러 값을 생산하는 generator로 바뀐다.

range는 상당히 많은 memory를 필요로한다.
```
def test_range():
    for _ in range(1, 100000000):
        pass

def test_xrange():
    for _ in xrange(1, 100000000):
        pass

[suker@suker-machine] ~/sukerGitHub/suker_python_project/highperfbook
$ python test5_1.py 
test_xrange time : 0.97509598732
test_range time : 2.63335514069
[suker@suker-machine] ~/sukerGitHub/suker_python_project/highperfbook
$ python test5_1.py 
test_range time : 2.61538720131
test_xrange time : 0.977447986603
```

<br>

문제점...
list에서 3의 배수가 몇개인지 알아보는 코드
```
divisible_by_three = len([n for n in list_of_numbers if n % 3 == 0])
```
list comprehension 문법을 사용했기 때문에 list_of_numbers에 있는 모든 3의 배수로 이루어진 리스트를 미리 생성한 다음, 필요한 계산을 수행한다. 3의 배수가 들어 있는 리스트는 쓸모가 없음에도...

#### list comprehension
[<값> for <항목> in <배열> if <조건> ] ==> 모든 <값>을 가지는 리스트를 생성한다.

#### gerator comprehension
[<값> for <항목> in <배열> if <조건> ] ==> generator에는 length 속성이 없다. 따라서 다음과 같이 수정한다.
```
divisible_by_three = sum((1 for n in list_of_numbers if n % 3 == 0))
```

<br>

memory 사용량 비교, 성능(시간)은 비슷하다.
```
def myxrange(start, stop, step=1):
    while start < stop:
        yield start
        start += step

@profile
def test_list_comprehension(list_of_numbers):
    divisible_by_three = len([n for n in list_of_numbers if n % 3 == 0])
    print(divisible_by_three)


@profile
def test_gen_comprehension(list_of_numbers):
    divisible_by_three = sum((1 for n in list_of_numbers if n % 3 == 0))
    print(divisible_by_three)


ff = myxrange(1,5000000)

test_list_comprehension(ff)
test_gen_comprehension(ff)
```
결과
```
$ python3 -m memory_profiler test5_1.py
Filename: test5_1.py

Line #    Mem usage    Increment   Line Contents
================================================
    20   31.176 MiB   31.176 MiB   @profile
    21                             def test_list_comprehension(list_of_numbers):
    22   95.926 MiB    0.668 MiB       divisible_by_three = len([n for n in list_of_numbers if n % 3 == 0])
    23   31.844 MiB  -64.082 MiB       print(divisible_by_three)


Filename: test5_1.py

Line #    Mem usage    Increment   Line Contents
================================================
    26   31.734 MiB   31.734 MiB   @profile
    27                             def test_gen_comprehension(list_of_numbers):
    28   31.734 MiB    0.000 MiB       divisible_by_three = sum((1 for n in list_of_numbers if n % 3 == 0))
    29   31.734 MiB    0.000 MiB       print(divisible_by_three)
```

<br>

#### 무한 급수와 iterator
지나온 상태 중 일부만 저장해두고 현재 값만 출력하면 되는 상황에서 generator가 아주 좋은 해법이 된다.
https://github.com/kchhero/suker_python_project/blob/master/highperfbook/test5_1_fibonacci.py
```
def fibonacci():
	i, j = 0, 1
	while True:
		yield j
		i, j = j, i + j
```

<br>

이터레이터를 사용하면 여러 CPU를 사용하거나 여러 대의 컴퓨터를 사용해서 문제를 해결하는코드를 작성하는 데
도움이 된다.

<br>

---

<br>

### ch6 Matrix, Vector

memory를 처음 할당하면 커널은 메모리에 대한 참조를 프로그램에 돌려주는 것 외에는 그다지 많은 일을 하지 않는다. 하지만 나중에 프로그램이 그 메모리를 처음 사용하면, OS는 minor page fault 인터럽트를 발생시켜 실행 중인 프로그램을 잠시 멈추고 실제 필요한 메모리를 할당한다. - lazy allocation

프로그램이 어떤 장치 (disk, network)에서 읽지 못한 데이터를 요청하면 major page fault가 발생한다.

<br>

#### numpy
데이터를 연속된 메모리 공간에 저장하며 이에 대한 벡터 연산을 지원한다.
numpy array에 대해 수행하는 산술 연산은 개별 항목을 하나씩 순회하며 처리할 필요가 없다.
행렬 연산이 간단해지며, 빠르다.

<br>

#### 메모리 할당과 제자리 연산
- 메모리 할당은 cache miss 보다 더 나쁘다. 메모리 할당은 캐시에서 필요한 데이터를 찾지 못하면 단순히 RAM을 찾아보는 대신, 필요한 크기만큼의 데이터를 운영 체제에 요청한 다음 예약해둔다. OS에 무언가를 요청하는 행위는 cache를 채우는 것과는 비교가 안 될 정도로 큰 오버헤드를 초래한다.

- 제자리 연산(in-place operation)은 +=, \*= 처럼 입력 중 하나를 결과값을 저장하는 용도로 사용하는 연산을 말한다.
제자리 연산은 계산 결과를 저장하기 위한 메모리 공간을 따로 할당하지 않는다.

