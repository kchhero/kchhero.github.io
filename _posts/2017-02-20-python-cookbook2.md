---
title: 'Python CookBook3판 - O REILLY Study(ch3)'
layout: post
tags:
  - python
category: programming
---
#### 3.9 큰 배열 계산

배열 연산시 큰 계산이 필요한 경우는 numpy 가 효율적이다.
아래는 list와 numpy의 차이점 ==>
```python?line_number=false
x = [1,2,3,4]
y = [5,6,7,8]
x*2      =>   [1,2,3,4,1,2,3,4]
```
```python?line_number=false
import numpy as np
ax = np.array([1,2,3,4])
ay = np.array([5,6,7,8])

ax*2    =>   array([2,4,6,8])
```
행열과 벡터를 다루려면 NumPy 로....


NumPy는 배열에 사용 가능한 "일반 함수(universal function)"를 제공한다. 
```python?line_number=false
np.sqrt(ax) ==> [ 1.          1.41421356  1.73205081  2.        ]
```
배열 요소를 순환하며 요소마다 math 모듈 함수로 계산하는 것보다 수백 배 빠르다.

==> 실제로 해보자... --> 무식한 방법인듯하다. test중인 spyder 가 맛이 갔다. 하지만 책에서 말하고자 하는뜻은 확인 되었다.

```python?line_number=false
import numpy as np
import math
import time

myArray = range(1,999999)

npX = np.array(myArray)

def testNP() :
tt = time.time()
temp = np.sqrt(npX)
print "testNP Done, lastValue = %.5f"%temp[-1]
print "test Time = %.3f"%(time.time() - tt)

def testMath() :
tt = time.time()
temp = 0
for i in myArray : #for i in npX :
temp = math.sqrt(i)

print "testMath Done, lastValue = %.5f"%temp
print "test Time = %.3f"%(time.time() - tt)

testNP()
testMath()
```
```python?line_number=false
결과 : 
testNP Done, lastValue = 999.99900
test Time = 0.109
testMath Done, lastValue = 999.99900
test Time = 0.343 ==> for i in npX :

testNP Done, lastValue = 999.99900
test Time = 0.093
testMath Done, lastValue = 999.99900
test Time = 0.171 ==> for i in myArray :
```

---

#### 3.11 임의의 요소 뽑기

```python?line_number=false
import random

values = [1,2,3,4,5,6]
random.choice(values) ==> 임의의 아이템 선택
random.sample(values,2) ==> 임의의 아이템을 N개 뽑아서 버릴 목적일때
random.shuffle(values) ==> 아이템을 무작위로 섞을때
random.randint(0,10) ==> 임의의 정수를 생성
random.random() ==> 0과 1 사이의 균등 부동 소수점 값을 생성
```

"random 모듈은 Mersenne Twister 알고리즘을 사용해 난수를 발생시킨다."
==> wiki 에서 찾아보니 아래와 같은 설명이 있다. 참고...

---

메르센 트위스터
위키백과, 우리 모두의 백과사전.
메르센 트위스터(Mersenne Twister)는 1997년에 마츠모토 마코토(松本 眞)와 니시무라 다쿠지(西村 拓士)가 개발한 유사난수 생성기이다.[1] 메르센 트위스터는 동일한 저자들이 개발한 TT800 생성기의 개선판으로, 기존 생성기들의 문제점들을 피하면서 매우 질이 좋은 난수를 빠르게 생성할 수 있도록 설계되었다.
메르센 트위스터의 이름은 난수의 반복 주기가 메르센 소수인 데에서 유래했다. 메르센 트위스터는 그 속도와 난수의 품질 때문에 점점 많은 곳에서 채택되고 있으며, 흔히 주기가 2^{19937}-1인 MT19937을 사용한다. MT19937과 같으나 생성해 내는 난수가 32비트가 아닌 64비트인 MT19937-64도 쓰이며,2006년에 동일한 저자들이 발표한 SIMD 기반 메르센 트위스터는 MT19937에 비해 대략 두 배 정도 빠른 것으로 알려져 있다.
난수의 품질에도 불구하고, 메르센 트위스터는 암호학적으로 안전한 유사난수 생성기가 아니다. 즉 난수의 특성(주기, 난수 범위)을 알고 있을 때 유한한 수의 난수(이 경우 624개)만으로 현재 생성기의 상태를 알아 낼 수 있으며, 그 뒤에 나올 난수를 예측해 낼 수 있다. 암호학적으로 안전한 유사난수 생성기를 얻기 위해서는 해시 함수를 사용해야 하지만 난수의 생성 속도가 낮아진다. 또는 블룸 블룸 슙(BBS)과 같이 암호학적으로 안전하게 설계된 생성기를 쓸 수도 있다.
http://ko.wikipedia.org/wiki/%EB%A9%94%EB%A5%B4%EC%84%BC_%ED%8A%B8%EC%9C%84%EC%8A%A4%ED%84%B0