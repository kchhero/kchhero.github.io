---
category: programming
layout: post
tags:
  - python
title: Python-numpy
---
#### numpy
Machine Learning 을 공부하는 과정에서 가장 많이 접하는 python library 가 numpy인듯하다.
numpy의 생소한 module 들에 대해서 정리하고자 한다.

<br>

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

---

##### numpy.randn
randn 명령은 표준 정규 분포 값을 균일하게 생성한다.
randn: Gaussian normal
![Gaussian Normal](https://github.com/kchhero/kchhero.github.io/blob/master/assets/ext_images/python_images/gaussian_normal.png?raw=true)
```
np.random.randn(2,3)

[[ 1.90126345  0.23773306 -2.06282222]
 [-0.83982942 -2.15618793  2.49362771]]
 ```