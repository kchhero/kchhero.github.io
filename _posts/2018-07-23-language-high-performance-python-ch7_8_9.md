---
categories: Uncategoried
category: programming
layout: post
tags:
  - Language
title: 'Python> High Performance Python(4)'
---
summary : chapter7,8,9 내용 정리/요약, cython / concurrency / multiprocessing

### ch7 cython
#### ctypes

---

### ch8 동시성
https://github.com/kchhero/high_performance_python/tree/master/08_concurrency

#### 비동기 프로그래밍
- 간단한 이벤트 루프
```
from queue import Queue
# from functools import partial
# -
eventloop = None
#
class EventLoop(Queue):
    def start(self):
        while True:
            function = self.get()
            function()
# -
def do_hello():
    global eventloop
    print("Hello")
    eventloop.put(do_world)
# -
def do_world():
    global eventloop
    print("World")
    eventloop.put(do_hello)
# -
if __name__ == "__main__":
    eventloop = EventLoop()
    eventloop.put(do_hello)
    eventloop.start()
```
- 이벤트 루프를 asynchronous I/O 연산과 결합하면 I/O 작업을 수행하면서 엄청난 성능 향상을 얻을 수 있다.

<br>
<br>

#### Callback, Future
event loop를 사용하는 프로그래밍에는 callback과 future의 두 가지 형태가 있다.
- 콜백 패러다임에서는 각 함수를 호출할 때 *callback*이라는 인자를 넘긴다. 불려진 함수는 값을 반환하는 대신, 그 값을 인자로 실어 콜백 함수를 호출한다.
- 퓨처를 실제 결과가 아닌 미래에 얻을 결과를 담을 promise를 반환하는 비동기 함수다. 비동기 함수가 반환하는 퓨처가 완료되어 필요한 값이 채워지기를 기다려야 한다.

<br>
<br>

#### sequencial crawler
- 각 요청에 0.1초가 걸리고 요청을 500번 보냈으므로, 전체 요청을 처리하는데 약 50초 걸릴것이다.
```
import requests
import string
import random
#.
def generate_urls(base_url, num_urls):
    for i in range(num_urls):
        yield base_url + "".join(random.sample(string.ascii_lowercase, 10))
#.
def run_experiments(base_url, num_iters=500):
    response_size = 0
    for url in generate_urls(base_url, num_iters):
        response = requests.get(url)
        response_size += len(response.text)
    return response_size
#.
if __name__ == "__main__":
    import time
    delay = 100
    num_iter = 500
    base_url = "http://127.0.0.1:8080/add?name=serial&delay={}&".format(delay)
#.
    start = time.time()
    result = run_experiments(base_url, num_iter)
    end = time.time()
    print("Result: {}, Time: {}".format(result, end - start))
```
- 결과
```
$ python3 test8_2.py 
Result: 500, Time: 52.93217206001282
```

<br>
<br>

#### gevent
가장 단순한 비동기 라이브러리 중 하나.

<br>

#### tornado
페이스북에서 개발한 비동기 I/O 패키지.

<br>
<br>

#### AsyncIO
파이썬 3.4 부터 이전의 asyncio 표준 라이브러리 모듈을 개선했다. gevent와 tornado 방식의 동시성에서 영향을 받았다. 코루틴을 더 간단하게 다룰 수 있도록 yield from 이라는 키워드를 추가했다.

<br>

---

<br>

### ch9 multiprocessing
- 문제를 여러 CPU로 병렬화한다면, n 코어 시스템에서 n배의 속도 향상을 기대 할 수 있다?
프로세스를 추가하면 통신에 따른 부가 비용이 늘어나며 한 프로세스가 사용할 수 있는 RAM이 줄어들게되므로 실제 n배의 속도 향상을 온전히 얻을 수는 없다.
- multiprocessing 모듈로 처리할 수 있는 작업의 예
		CPU 위주의 작업을 process나 pool 객체를 사용해 병렬화
		dummy 모듈을 사용해서 I/O 위주의 작업을 스레드를 사용하는 Pool로 병렬화
		Queue를 통해  pickling한 결과를 공유
		병렬화한 작업자 사이에서 바이트,원시 데이터 타입, 사전, 리스트 등의 상태를 공유

<br>
<br>

#### multiprocessing 모듈
주 구성요소
- process : 현재 프로세스를 fork한 복사본.
- pool : process나 threading API를 wrapping 하여 작업을 공유하고, 합쳐진 결과를 반환해주는 사용하기 편리한 worker pool로 만든다.
- queue  : 여러 producer와 conosumer가 사용할 수 있게 해주는 FIFO 대기열이다.
- pipe : 두 프로세스 사이의 통신 채널
- manager : process간에 파이썬 객체를 공유하기 위한 고수준의 managed interface.
- ctypes : process를 fork한 다음 여러 process가 원시 데이터 타입을 공유 할 수 있게 해준다.

<br>
<br>

*몬테카를로 원주율 구하기 알고리즘*
```
import random

TESTCNT = 100000000

def monte_carlo():
    circleIn = 0

    for i in range(TESTCNT):
        tempX = random.random()
        tempY = random.random()
        if (tempX*tempX + tempY*tempY) <= 1:
            circleIn += 1

    print("shot cnt = %f" % (circleIn/TESTCNT))
    print("pi = %f" % (4.0*circleIn/TESTCNT))

startTime = time.time()
monte_carlo()
print("running time : " + str(time.time()-startTime))
```
결과
```
$ python3 test9_2.py
shot cnt = 0.785400
pi = 3.141602
running time : 25.7069725990295
```

<br>
<br>

#### process와 thread를 사용해 원주율 추정하기
##### 파이썬 객체 사용, numpy사용
```
"""Estimate Pi using Threads and Processes"""
import time
import numpy as np
from multiprocessing import Pool


def estimate_nbr_points_in_quarter_circle(nbr_samples):
   # 각각의 새로운 프로세스에서 numpy를 위한 난수 시드를 생성한다.
   # 그렇제 않다면 fork로 만들어진 모든 프로세스가 동일한 상태를 공유한다.
    np.random.seed()
    xs = np.random.uniform(0, 1, nbr_samples)
    ys = np.random.uniform(0, 1, nbr_samples)
    estimate_inside_quarter_unit_circle = (xs * xs + ys * ys) <= 1
    nbr_trials_in_quarter_unit_circle = np.sum(
        estimate_inside_quarter_unit_circle)
    return nbr_trials_in_quarter_unit_circle


if __name__ == "__main__":
    nbr_samples_in_total = 100000000
    nbr_parallel_blocks = 8

    pool = Pool()

    nbr_samples_per_worker = int(nbr_samples_in_total / nbr_parallel_blocks)
    print(type(nbr_samples_per_worker))
    print("Making {} samples per worker".format(nbr_samples_per_worker))

    # confirm we have an integer number of jobs to distribute
    assert nbr_samples_per_worker == int(nbr_samples_per_worker)
    nbr_samples_per_worker == int(nbr_samples_per_worker)
    map_inputs = [nbr_samples_per_worker] * nbr_parallel_blocks
    print(type(map_inputs))
    t1 = time.time()
    results = pool.map(estimate_nbr_points_in_quarter_circle, map_inputs)
    pool.close()
    print("Dart throws in unit circle per worker:", results)
    print("Took {}s".format(time.time() - t1))
    nbr_in_circle = sum(results)
    combined_nbr_samples = sum(map_inputs)

    pi_estimate = float(nbr_in_circle) / combined_nbr_samples * 4
    print("Estimated pi", pi_estimate)
    print("Pi", np.pi)
```

<br>
<br>

#### Understanding the Python GIL
- GIL (Global Interpreter Lock)

참고 : http://highthroughput.org/wp/cb-1136/

파이썬의 스레드는 I/O 위주의 작업에는 훌륭히 작동하지만, CPU 위주의 문제에는 좋은 선택이 못된다.
데이비드 비즐리, The Python GIL Visualized 참고.
refs : http://dabeaz.blogspot.com/2010/01/python-gil-visualized.html
![](http://www.dabeaz.com/images/GILLegend.png)
![](http://www.dabeaz.com/images/GIL_2cpu.png)
멀티코어 시스템에서 다중 스레드를 사용하는 경우에만 문제가 된다.

여러 스레드를 실행하는 단일 코어 시스템에서는 GIL전투가 벌어지지 않는다.
![](http://www.dabeaz.com/images/GIL_1cpu.png)

<br>
<br>

#### Prime Number
프로세스간에 공유해야 할 상태가 전혀 없는 매우 병렬적인 문제.

chunksize 매개변수의 사용 -> 작업 단위의 크기가 소수 판정 속도에 미치는 영향

** sentinel **

```
import math
import time
import multiprocessing
from multiprocessing import Pool

FLAG_ALL_DONE = b"WORK_FINISHED"
FLAG_WORKER_FINISHED_PROCESSING = b"WORKER_FINISHED_PROCESSING"


def check_prime(possible_primes_queue, definite_primes_queue):
    while True:
        n = possible_primes_queue.get()
        if n == FLAG_ALL_DONE:
            definite_primes_queue.put(FLAG_WORKER_FINISHED_PROCESSING)
            break
        else:
            if n % 2 == 0:
                continue
            for i in range(int(math.sqrt(n) + 1), 2):
                if n % i == 0:
                    break

            else:
                definite_primes_queue.put(n)


if __name__ == "__main__":
    primes = []

    manager = multiprocessing.Manager()
    # We could limit the input queue size with e.g. `maxsize=3`
    possible_primes_queue = manager.Queue()
    definite_primes_queue = manager.Queue()

    NBR_PROCESSES = 4
    pool = Pool(processes=NBR_PROCESSES)
    processes = []

    for _ in range(NBR_PROCESSES):
        p = multiprocessing.Process(
            target=check_prime,
            args=(
                possible_primes_queue,
                definite_primes_queue))
        processes.append(p)
        p.start()

    t1 = time.time()
    number_range = range(1, 1000000)  # A
    # number_range = xrange(100000000, 100100000)  # B
    # number_range = range(100000000, 101000000)  # C
    # number_range = xrange(1000000000, 1000100000)  # D
    # number_range = xrange(100000000000, 100000100000)  # E

    for possible_prime in number_range:
        possible_primes_queue.put(possible_prime)
    
    print("ALL JOBS ADDED TO THE QUEUE")

    # add poison pills to stop the remote workers
    for n in range(NBR_PROCESSES):
        possible_primes_queue.put(FLAG_ALL_DONE)

    print("NOW WAITING FOR RESULTS...")
    processors_indicating_they_have_finished = 0
    while True:
        # block whilst waiting for results
        new_result = definite_primes_queue.get()
        if new_result == FLAG_WORKER_FINISHED_PROCESSING:
            print("WORKER {} HAS JUST FINISHED".format(processors_indicating_they_have_finished))
            processors_indicating_they_have_finished += 1
            if processors_indicating_they_have_finished == NBR_PROCESSES:
                break
        else:
            primes.append(new_result)
    assert processors_indicating_they_have_finished == NBR_PROCESSES

    print("Took:", time.time() - t1)
    print(len(primes), primes[:10], primes[-10:])
```

결과
```
$ python3 test9_3_prime_sentinel.py 
ALL JOBS ADDED TO THE QUEUE
NOW WAITING FOR RESULTS...
WORKER 0 HAS JUST FINISHED
WORKER 1 HAS JUST FINISHED
WORKER 2 HAS JUST FINISHED
WORKER 3 HAS JUST FINISHED
Took: 199.00096464157104
500000 [1, 3, 5, 7, 9, 11, 13, 15, 17, 19] [999981, 999983, 999985, 999987, 999989, 999991, 999993, 999995, 999997, 999999]
```

<br>
<br>

#### 프로세스간 통신을 사용

** 순차적 해법 **
https://github.com/kchhero/high_performance_python/blob/master/09_multiprocessing/prime_validation/primes.py
```
def check_prime(n):
    if n % 2 == 0:
        return False
    from_i = 3
    to_i = math.sqrt(n) + 1
    for i in xrange(from_i, int(to_i), 2):
        if n % i == 0:
            return False
return True
```

<br>

** 단순한 풀 해법 **
https://github.com/kchhero/high_performance_python/blob/master/09_multiprocessing/prime_validation/primes_pool_per_number1.py
```
def check_prime(n, pool, nbr_processes):
    from_i = 3
    to_i = int(math.sqrt(n)) + 1
    ranges_to_check = create_range.create(from_i, to_i, nbr_processes)
    ranges_to_check = zip(len(ranges_to_check) * [n], ranges_to_check)
    assert len(ranges_to_check) == nbr_processes
    results = pool.map(check_prime_in_range, ranges_to_check)
    if False in results:
        return False
return True
```

<br>

** 조금 덜 단순한 풀 해법 **
https://github.com/kchhero/high_performance_python/blob/master/09_multiprocessing/prime_validation/primes_pool_per_number2.py
가능성이 높은 인수는 순차적으로 검사하여 빠르게 처리하고, 큰 값은 인수들은 병렬로 처리한다.
```
def check_prime(n, pool, nbr_processes):
    # cheaply check high probability set of possible factors
    from_i = 3
    to_i = 21
    if not check_prime_in_range((n, (from_i, to_i))):
        return False

    from_i = to_i
    to_i = int(math.sqrt(n)) + 1
    ranges_to_check = create_range.create(from_i, to_i, nbr_processes)
    ranges_to_check = zip(len(ranges_to_check) * [n], ranges_to_check)
    assert len(ranges_to_check) == nbr_processes
    results = pool.map(check_prime_in_range, ranges_to_check)
    if False in results:
        return False
return True
```

<br>

** Manager.Value 플래그 사용하기 **
고수준 파이썬 객체를 프로세스 간에 managed 공유 객체로 활용할 수 있다. 저수준 객체들은 proxy 객체로 감싼다.
안정성을 보장하게되고 속도는 느려지지만 유연성을 얻을 수 있다. 
https://github.com/kchhero/high_performance_python/blob/master/09_multiprocessing/prime_validation/primes_pool_per_number_manager.py

<br>

** 레디스를 플래그로 사용하기 **
https://github.com/kchhero/high_performance_python/blob/master/09_multiprocessing/prime_validation/primes_pool_per_number_redis.py
* redis : 인메모리 키/값 저장소 엔진이다. 자체 락을 제공하며, 각 연산은 원자적이다. 언어와 무관한 데이터 저장소를 만들 수 있다. 
* 저장 : 문자열 리스트, 문자열의 집합, 문자열을 정렬한 집합, 문자열의 해시
==> https://www.joinc.co.kr/w/man/12/REDIS/IntroDataType

<br>

** RawValue를 플래그로 사용하기 **

** mmap를 플래그로 사용하기 **
바이트들을 공유하는 가장 빠른 방식이다.
https://github.com/kchhero/high_performance_python/blob/master/09_multiprocessing/prime_validation/primes_pool_per_number_mmap.py

<br>
<br>

#### multiprocessing 과 numpy 데이터 공유하기

