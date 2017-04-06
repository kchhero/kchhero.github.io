---
title: Python-numpy
layout: post
tags:
  - python
category: programming
---
#### numpy

##### numpy.argmax

axis에 대해서 detail하게 살펴본다.

argmax(x,axis=0) 인 경우는 각각의 열 중에서 가장 큰 값의 index를 얻어온다.
argmax(x,axis=1) 인 경우는 각각의 행에 대해서 가장 큰 값의 index를 얻어온다.

아래 예제를 보면 직관적으로 알 수 있다.

argmin은 반대로 가장 작은 값을 찾는다.

```python
import numpy as np

x = np.array([[0.9, 0.8, 0.1],[0.1, 0.8, 0.1], [0.3, 0.1, 0.6], [0.2, 0.5, 0.3],[0.2, 0.1, 0.3], [0.8, 0.1, 0.1]])
print(x)
p = np.argmax(x, axis=0)
t = np.argmax(x, axis=1)
print(p)
print(t)

[[ 0.9  0.8  0.1]
 [ 0.1  0.8  0.1]
 [ 0.3  0.1  0.6]
 [ 0.2  0.5  0.3]
 [ 0.2  0.1  0.3]
 [ 0.8  0.1  0.1]]
[0 0 2]
[0 1 2 1 2 0]
```