---
title: 'Python> High Performance Python(2)'
layout: post
tags:
  - Language
category: programming
---
summary : chapter3,4 내용 정리/요약, list / tuple, dict / set

### ch3 list, tuple

#### list
list.index()의 알고리즘은 아래와 같다. 최악의 경우 O(n)의 사간 복잡도를 갖는다.
```
def linear_search(needle, array):
    for i, item in enumerate(array):
	    if item == needle:
		    return i
	return -1
```
뭔가 효율적인 탐색이 필요하다.

* __eq__, __lt__ 매직 함수의 사용,
* bisect 모듈 이용.

<br>

#### list와 tuple 비교
1. 리스트는 동적 배열이다. 
2. 튜플은 정적 배열이다.
3. 튜플은 파이썬 런타임에서 캐싱하므로 사용할 때마다 커널에 메모리를 요청하지 않아도 된다.

#### list의 초과 할당
- 크기가 N인 꽉찬 리스트에 새로운 항목을 추가하면 파이썬은 원래 담고 있던 N개와 새로 추가한
항목까지 모두 담기에 충분한 크기의 새로운 리스트를 생선한다. N+1 크기를 할당하는것이 아니다.
리스트에 한 번 추가하면 그 뒤로도 여러 번 더 추가할 확률이 높기 때문이다.
이전 리스트의 데이터를 모두 새로운 리스트로 복사하고 이전 리스트는 삭제한다.

#### tuple
- 튜플은 내용을 바꾸거나 크기를 변경 할 수 없다. 그러나 두 튜플을 하나의 새로운 튜플로 합칠 수는 있다.
리스트의 resize와 비슷하지만 리스트와는 달리 여유 공간을 더 할당하지 않는다.
- 두 튜플을 합치면 항상 새로운 튜플을 위한 메모리를 새로 할당한다. (기존 튜플은 여전히 남아있다.)
- 리소스 캐싱 : 파이썬이 GC를 수행할 때 크기가 20 이하인 튜플은 즉시 회수하지 않고 나중을 위해 저장해둔다.
같은 크기의 튜플이 나중에 다시 필요해지면 OS로부터 메모리를 새로 할당받지 않고, 기존에 할당해둔 메모리를
재사용한다는 뜻이다.


<br>
<br>

### ch4 dictionary, set

#### dict와 set 비교
dict와 set은 거의 동일하지만 set은 값을 가지지 않는다, set은 유일한 key를 저장하는 자료구조다.
내재적 순서가 없는 list와 tuple은 최적의 경우 O(log n) 시간 복잡도로 값을 찾는다. 반면 dict와 set은 O(1) 이다.

#### 동작 원리
hash table을 사용해서 O(1)의 시간 복잡도를 가진다. 임의의 key를 list의 index로 변환하는 hash 함수를 사용한 결과다. data의 key를 list의 index처럼 사용할 수 있도록 변환하는 작업을 통해 list와 동일한 성능을 얻을 수 있다.

custom hash 함수
```
class City(str):
    def __hash__(self):
        print(ord(self[0]))
        return ord(self[0])


data = {
    City("Rome"): 4,
    City("San Francisco"): 3,
    City("New York"): 5,
    City("Barcelona"): 2,
}
```

<br>

#### 크기 변경
크기를 변경할 때에는 충분히 큰 해시 테이블을 할당하고 그 크기에 맞게 마스크를 조정한다.그리고 모든 항목을 새로운 해시 테이블로 옮긴다. 이 과정에서 바뀐 마스크 때문에 색인을 새로 계산해야 한다.
dict / set의 최소 크기는 8이다. 해시 테이블은 줄어들기도 한다. 크기 변경은 삽입 연산중에만 발생한다.
- 8, 32, 128, 512, 2048, 8192, 32768, 131072, 262144 ...  --> 4배씩 증가한다.50,000 이후로는 2배씩

#### namespace
파이썬에서 변수, 함수, 모듈이 사용될 때 그 객체를 어디서 찾을지 결정하는 계층이 있다.
모든 지역 변수를 담고 있는 locals() 배열을 찾는다 다음으로 globals() dictionary에서 찾고 마지막으로
 __builtin__ 객체에서 찾는다.

```
import math
from math import sin

def test1(x):
    return math.sin(x)

def test2(x):
    return sin(x)

def test3(x, sim=math.sin):
    return sin(x)

```
dis assem 하면 아래와 같다.
https://github.com/kchhero/suker_python_project/blob/master/highperfbook/test4_2_namespace_dis.py
```
test1
  6           0 LOAD_GLOBAL              0 (math)   #사전 탐색
              3 LOAD_ATTR                     1 (sin)     #사전 탐색
              6 LOAD_FAST                     0 (x)        #지역 변수 탐색
              9 CALL_FUNCTION            1
             12 RETURN_VALUE        
test2
 10         0 LOAD_GLOBAL                0 (sin)     #사전 탐색
              3 LOAD_FAST                     0 (x)        #지역 변수 탐색
              6 CALL_FUNCTION            1
              9 RETURN_VALUE        
test3
 14           0 LOAD_GLOBAL              0 (sin)    #지역 변수 탐색
              3 LOAD_FAST                     0 (x)        #지역 변수 탐색
              6 CALL_FUNCTION            1
              9 RETURN_VALUE        
```
test2의 sin 함수는 전역 namespace에서 직접 접근할 수 있다. math 모듈을 찾은 다음 모듈의 속성을 탐색하는
과정을 피할 수 있다. 

```
from math import sin
import time


def testloop1(iterations):
    result = 0
    for i in range(iterations):
        result += sin(i)
    print(result)


def testloop2(iterations):
    result = 0
    local_sin = sin
    for i in range(iterations):
        result += local_sin(i)
    print(result)


t1 = time.time()
testloop1(100000)
print("testloop1 : %s" % str(time.time()-t1))

t1 = time.time()
testloop2(100000)
print("testloop2 : %s" % str(time.time()-t1))
```
결과
```
$ python test4_2_namespace2.py 
1.81202830566
testloop1 : 0.0120189189911
1.81202830566
testloop2 : 0.0086190700531
```
