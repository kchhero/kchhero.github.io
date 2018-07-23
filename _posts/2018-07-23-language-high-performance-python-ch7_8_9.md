---
title: 'Python> High Performance Python(4)'
layout: post
tags:
  - Language
category: programming
---
summary : chapter7,8,9 내용 정리/요약, cython / concurrency / multiprocessing

### ch7 cython
#### ctypes

### ch8 동시성
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
