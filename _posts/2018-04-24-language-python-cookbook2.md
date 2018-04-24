---
title: 'Python> Python CookBook3판 - O REILLY Study(ch13) 유틸리티'
layout: post
tags:
  - Language
category: programming
---
#### 13.4 암호 입력받기

```python?line_number=false
import getpass

user = input('Enter your username: ')

user = getpass.getuser()
passwd = getpass.getpass()

print(user)
print(passwd)
```

<br>

#### 13.6 외부 명령을 실행하고 결과 얻기

```python?line_number=false
import subprocess

try :
    out_bytes = subprocess.check_output(['netstat','-h'])
    print(out_bytes)
    #byte to text
    out_text = out_bytes.decode('utf-8')
    print(out_text)

except subprocess.CalledProcessError as e :
    out_bytes = e.output
    code = e.returncode
```

<br>

#### 13.9 이름으로 파일 찾기

```python?line_number=false
import os
import sys

def findfile(start, name) :
    for relpath, dirs, files in os.walk(start) :
        if name in files:
            full_path = os.path.join(start,relpath, name)
            print(os.path.normpath(os.path.abspath(full_path)))

if __name__ == '__main__' :
    findfile(sys.argv[1], sys.argv[2])
```
* os.walk() 는 디렉토리를 순환하며 각 디렉토리마다 튜플을 반환한다.
* os.path.normpath() --> path를 normal화하고, 슬래시가 두 개 나오는 문제와 현재 디렉토리를 여러 번 참조하는 문제 등을 해결한다.

<br>

* 최근 변경된 파일의 목록을 출력한다.

```python?line_number=false
import os
import sys
import time

def modified_within(top, seconds) :
    now = time.time()
    for path, dirs, files in os.walk(top) :
        for name in files :
            fullpath = os.path.join(path, name)
            if os.path.exists(fullpath) :
                mtime = os.path.getmtime(fullpath)
                if mtime > (now - seconds):
                    print(fullpath)

if __name__ == '__main__' :
    if len(sys.argv) != 3 :
        print('Usage: {} dir seconds'.format(sys.argv[0]))
        raise SystemExit(1)

    modified_within(sys.argv[1], float(sys.argv[2]))
```