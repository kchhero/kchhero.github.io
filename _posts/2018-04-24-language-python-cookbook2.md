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

```python?line_number=false
...
from concurrent import futures
...

def find_all_robots(logdir) :
    files = glob.glob(logdir+'/*.gz')
    all_robots = set()
    with futures.ProcessPoolExecutor() as pool :
        for robots in pool.map(find_robots, files) :
            all_robots.update(robots)

    return all_robots
```

* 쿼드코어 기준 3.5배 빠르다.

* ProcessPoolExecutor의 일반적인 사용 방법

```
from concurrent.futures import ProcessPoolExecutor

with ProcessPoolExecutor() as pool :
	...
	do work in rarallel using pool
	...

# 많은 작업을 하는 함수
def work(x) ;
	...
	return result
	

# 병렬화하지 않은 코드
results = map(work, data)

# 병렬화 코드
with ProcessPoolExecutor() as pool :
	results = pool.map(work, data)

```

<br>

#### 12.14 Unix에서 데몬 프로세스 실행

```python?line_number=false
#!/usr/bin/env python3
#  daemon.py

import os
import sys
import atexit
import signal

def daemonize(pidfile, *, stdin='/dev/null',
                          stdout='/dev/null',
                          stderr='/dev/null'):
    if os.path.exists(pidfile) :
        raise RuntimeError('Already running')

    # first fork
	# 부모에 의한 즉각적인 종료, 자식 프로세스를 잃고 나서 os.setsid() 호출을 통해 완전히 새로운 프로세스를 생성하고 자식을 리더로 설정한다.
	# 데몬을 터미널에서 올바르게 때어내고(부모 프로세스로부터 분리), 신호와 같은 것이 이런 작업을 방해하지 않도록 보장한다.

    try :
        if os.fork() > 0 :
            raise SystemExit(0)  #parent exit
    except OSError as e :
        raise RuntimeError('fork #1 failed')

    # 현재 작업 디렉터리와 파일 모드 마스크를 재설정한다.
	# 디렉토리를 변경하면 데몬을 실행한 디렉토리에서 더 이상 동작하지 못하도록 하므로...
    os.chdir('/')
    os.umask(0)
    os.setsid()

    # second fork
	# 데몬 프로세스가 새로운 제어 터미널을 취득하지 못하도록 하고 더 높은 고립을 제공한다.
    try :
        if os.fork() > 0 :
            raise SystemExit(0)
    except OSError as e :
        raise RuntimeError('fork #2 failed')

    # input,output buffer flush
    sys.stdout.flush()
    sys.stderr.flush()

    # stdin, stdout, stderr file descriptor replace
    with open(stdin, 'rb', 0) as f :
        os.dup2(f.fileno(), sys.stdin.fileno())
    with open(stdout, 'ab', 0) as f :
        os.dup2(f.fileno(), sys.stdout.fileno())
    with open(stderr, 'ab', 0) as f :
        os.dup2(f.fileno(), sys.stderr.fileno())

    #PID file write
    with open(pidfile, 'w') as f:
        print(os.getpid(), file=f)

    # when exit, PID file remove
	# 파이썬 인터프리터가 종료될 때 실행하기 위한 함수를 등록한다.
    atexit.register(lambda: os.remove(pidfile))

    # handler about exit
    def sigterm_handler(signo, frame) :
        raise SystemExit(1)

    signal.signal(signal.SIGTERM, sigterm_handler)

def main() :
    import time
    sys.stdout.write('Daemon started with pid {}\n'.format(time.ctime()))
    time.sleep(10)

if __name__ == '__main__' :
    PIDFILE = '/tmp/daemon.pid'

    if len(sys.argv) != 2:
        print('Usage: {} [start|stop]'.format(sys.argv[0]), file=sys.stderr)
        raise SystemExit(1)

    if sys.argv[1] == 'start' :
        try :
            daemonize(PIDFILE,
                      stdout='/tmp/daemon.log',
                      stderr='/tmp/daemon.log')
        except RuntimeError as e :
            print(e, file=sys.stderr)
            raise SystemExit(1)

        main()

    elif sys.argv[1] == 'stop' :
        if os.path.exists(PIDFILE) :
            with open(PIDFILE) as f :
                os.kill(int(f.read()), signal.SIGTERM)
        else:
            print('Not running', file=sys.stderr)
            raise SystemExit(1)

    else :
        print('Unknown command {!r}'.format(sys.argv[1]), file=sys.stderr)
        raise SystemExit(1)

```
