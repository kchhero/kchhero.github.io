---
title: 'Python CookBook3판 - O REILLY Study(ch1)'       
layout: post
published: true
category: Programming
tags: Language
---

#### 1.3  deque 사용

```python?line_number=false
from collections import deque
	q = deque(maxlen=3)
	q.append(1)
	q.append(2)
	q.append(3)

>>> q
1,2,3

	q.appendLeft(4)
>>> q
4,1,2
```

==> maxlen을 설정하면 기존에 저장되어있던 값이 하나씩 밀린다. maxlen 설정없이 q=deque()   큐를 생성하면 크기가 가변적으로 늘어나거나 줄어든다.

```
q.appendLeft(4)
>>> q
4,1,2,3
```

==> 굳이 deque를 쓰는이유는?
책에서는 큐의 양끝에 item을 넣거나 빼는 작업에 시간복잡도 O(1)이 소요된다고 하고, list 작업에서는 O(N) 이 소요된다고 한다.

```python?line_number=false
from collections import deque
import time

def dequeTest() :
	startTime = time.time()
	q = deque()
	for i in range(40000000) :
		q.append(i)
	while len(q)!=0 :
		q.pop()
	print "deque time : %.5f"%(time.time() - startTime)

def listTest() :
	startTime = time.time()
	ll = []
	for i in range(40000000) :
		ll.append(i)
	while len(ll)!=0 :
		ll.pop(-1)
	print "list time : %.5f"%(time.time() - startTime)

dequeTest()
listTest()

결과 :
deque time : 8.72200
list time  : 15.35600
```

---


#### 1.4 N 아이템의 최대/최소값 찾기
원문 : "nlargest()와 nsmallest() 함수는 찾고자 하는 아이템의 개수가 상대적으로 작을 때 가장 알맞다."... "최소값이나 최대값을 구하려 한다면, min()과 max()를 사용하는 것이 더 빠르다."

```python?line_number=false
import heapq

nums = [1,2,3,-4,-5]
print heapq.nlargest(2,nums)
print heapq.nsmallest(2,nums)

==> 3,2
==> -5,-4
```
==> 정렬후 array의 0또는 -1 index를 찾는 원리
최대값이나 최소값만을 구하는것이면 min(), max()가 빠름
N의 크기가 컬렉션 크기와 비슷하면 정렬후 조각을 이용하는것이 빠름

==> heapq.py 를 살펴보면 아래와 같다. nlargest만 보자.

```python?line_number=false
def nlargest(n, iterable):
    """
	Find the n largest elements in a dataset.
    Equivalent to:  sorted(iterable, reverse=True)[:n]
    """
	if n < 0:
		return []

	it = iter(iterable)
	result = list(islice(it, n))

	if not result:
		return result

	heapify(result)
	_heappushpop = heappushpop

	for elem in it:
		_heappushpop(result, elem)

	result.sort(reverse=True)
	return result
```

==> 원문의 내용처럼 nlargest()와 nsmallest()의 실제 구현에서 sort를 사용하고 있다.

---

#### 1.12 시퀀스에 가장 많은 아이템 찾기
collections.Counter를 이용하면 편리하다
words = [ ... ]

```python?line_number=false
from collections import Counter

word_counts = Counter(words)
top_three = word_counts.most_common(3)
print(top_three)
```
==> dict를 사용하는 방식보다 훨씬 유용하다.
