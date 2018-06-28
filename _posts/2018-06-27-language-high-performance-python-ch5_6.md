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

xrange의 구현체
```
def xrange(start, stop, step=1):
    while start < stop:
        yield start
        start += step
```
값을 반환하는 대신 yield를 이용해 여러 값을 생산하는 generator로 바뀐다.

#### list
