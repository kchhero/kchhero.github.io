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

---

##### numpy.amax
numpy.amax(a, axis=None, out=None, keepdims=<class numpy._globals._NoValue at 0x40b6a26c>)[source]
Return the maximum of an array or maximum along an axis.
```
>>> a = np.arange(4).reshape((2,2))
>>> a
array([[0, 1],
       [2, 3]])
>>> np.amax(a)           # Maximum of the flattened array
3
>>> np.amax(a, axis=0)   # Maxima along the first axis
array([2, 3])
>>> np.amax(a, axis=1)   # Maxima along the second axis
array([1, 3])
```

---

##### numpy.nonzero
Return the indices of the elements that are non-zero.

A common use for nonzero is to find the indices of an array, where a condition is True. Given an array a, the condition a > 3 is a boolean array and since False is interpreted as 0, np.nonzero(a > 3) yields the indices of the a where the condition is true.
```
>>> x = np.eye(3)
>>> x
array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],
       [ 0.,  0.,  1.]])
>>> np.nonzero(x)
(array([0, 1, 2]), array([0, 1, 2]))

>>> a = np.array([[1,2,3],[4,5,6],[7,8,9]])
>>> a > 3
array([[False, False, False],
       [ True,  True,  True],
       [ True,  True,  True]], dtype=bool)
>>> np.nonzero(a > 3)
(array([1, 1, 1, 2, 2, 2]), array([0, 1, 2, 0, 1, 2]))
```