---
title: 'Python> Python CookBook3판 - O REILLY Study(ch12) 병렬처리'
layout: post
tags:
  - Language
category: programming
---
#### 12.1 스레드 시작과  정지

```python?line_number=false
import time

def countdown(n) :
    while n>0 :
        print('T-minus', n)
        n -= 1
        time.sleep(1)


from threading import Thread

t=Thread(target=countdown, args=(10,))
#t=Thread(target=countdown, args=(10,), daemon=True)
t.start()

if t.is_alive() :
    print('Still running')
else :
    print('Completed')
```
* daemon thread는 join할 수 없다. 하지만 메인 스레드가 종료될 때 자동으로 사라진다.
* 전역 인터프리터 락(global interpreter lock, GIL)으로 인해, 파이썬은 인터프리터에서 동시에 하나의 스레드만 실행 할 수 있다.
* 따라서 파이썬 스레드는 여러 CPU에 병렬적으로 동작하는 복잡한 작업에는 사용하지 않는 것이 좋다.
* 입출력 처리와 실행이 멈추는 작업을 수행하는 코드에서 병렬적 실행을 처리하는 것이 더 알맞다.

<br>

#### 12.4 Critical section Lock

```python?line_number=false
import threading

class SharedCounter:
    def __init__(self, initial_value=0) :
        self._value = initial_value
        self._value_lock = threading.Lock()

    def incr(self, delta=1) :
        # 이전 버전의 파이썬의 경우 acquire, release를 사용했음.
        # self._value_lock.acquire()
        # self._value += delta
        # self._value_lock.release()
        with self._value_lock :
            self._value += delta
            print(self._value)

    def decr(self, delta=1) :
        # 이전 버전의 파이썬의 경우 acquire, release를 사용했음.        
        # self._value_lock.acquire()
        # self._value -= delta
        # self._value_lock.release()        
        with self._value_lock :
            self._value -= delta
            print(self._value)
```
*  with 구문은 에러 발생을 줄이는 조금 더 고급스러운 표현 방식이다. release 를 깜빡하더라도 상관없다.

<br>

#### 12.8 간단한 병렬 프로그램

* map-reduce 스타일의 로그 분석 프로그램 ORG 버전은 아래와 같다.

```python?line_number=false
import gzip
import io
import glob

# [suker@suker-machine] ~/sukerGitHub/suker_python_project/cookbook/ch12
# $ ll logs
# total 148K
# drwxrwxr-x 2 suker suker  4K  4월 24 14:56 ./
# drwxrwxr-x 3 suker suker  4K  4월 24 14:56 ../
# -rw-r----- 1 suker suker 40K  4월 24 14:46 syslog.3.gz
# -rw-r----- 1 suker suker  3K  4월 24 14:46 syslog.4.gz
# -rw-r----- 1 suker suker 39K  4월 24 14:46 syslog.5.gz
# -rw-r----- 1 suker suker 13K  4월 24 14:46 syslog.6.gz
# -rw-r----- 1 suker suker 39K  4월 24 14:46 syslog.7.gz

def find_robots(filename) :
    robots = set()
    with gzip.open(filename) as f :
        for line in io.TextIOWrapper(f) :
            fields = line.split()
            if len(fields) >= 6 :
                if fields[5] == 'idVendor:' :
                    robots.add(fields[5])
                    robots.add(fields[6])
                    
    return robots

def find_all_robots(logdir) :
    files = glob.glob(logdir+'/*.gz')
    all_robots = set()
    for robots in map(find_robots, files) :
        all_robots.update(robots)
    return all_robots


if __name__ == '__main__' :
    robots = find_all_robots('logs')
    for ipaddr in robots :
        print(ipaddr)

```

==> 위 코드를 여러개의 CPU를 사용하도록 변경하면 다음과 같다.

