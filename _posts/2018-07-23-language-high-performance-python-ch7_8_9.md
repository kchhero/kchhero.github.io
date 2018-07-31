---
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

#### Callback, Future
event loop를 사용하는 프로그래밍에는 callback과 future의 두 가지 형태가 있다.
- 콜백 패러다임에서는 각 함수를 호출할 때 *callback*이라는 인자를 넘긴다. 불려진 함수는 값을 반환하는 대신, 그 값을 이니자로 실어 콜백 함수를 호출한다.
- 퓨처를 실제 결과가 아닌 미래에 얻을 결과를 담을 promise를 반환하는 비동기 함수다. 비동기 함수가 반환하는 퓨처가 완료되어 필요한 값이 채워지기를 기다려야 한다.

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

#### gevent
가장 단순한 비동기 라이브러리 중 하나.

<br>

#### tornado
페이스북에서 개발한 비동기 I/O 패키지.

<br>

#### AsyncIO
파이썬 3.4 부터 이전의 asyncio 표준 라이브러리 모듈을 개선했다. gevent와 tornado 방식의 동시성에서 영향을 받았다. 코루틴을 더 간단하게 다룰 수 있도록 yield from 이라는 키워드를 추가했다.

---

### ch9 multiprocessing
- 문제를 여러 CPU로 병렬화한다면, n 코어 시스템에서 n배의 속도 향상을 기대 할 수 있다?
프로세스를 추가하면 통신에 따른 부가 비용이 늘어나며 한 프로세스가 사용할 수 있는 RAM이 줄어들게되므로 실제 n배의 속도 향상을 온전히 얻을 수는 없다.
- multiprocessing 모듈로 처리할 수 있는 작업의 예
		CPU 위주의 작업을 process나 pool 객체를 사용해 병렬화
		dummy 모듈을 사용해서 I/O 위주의 작업을 스레드를 사용하는 Pool로 병렬화
		Queue를 통해  pickling한 결과를 공유
		병렬화한 작업자 사이에서 바이트,원시 데이터 타입, 사전, 리스트 등의 상태를 공유

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

monte_carlo()
```
결과
```
$ python3 test9_2.py
shot cnt = 0.785400
pi = 3.141602
```

<br>

#### process와 thread를 사용해 원주율 추정하기
##### 파이썬 객체 사용
