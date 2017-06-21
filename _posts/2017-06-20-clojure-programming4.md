---
title: programming-clojure-ch4
layout: post
tags:
  - clojure
category: language
---
### data를 sequence로 다루기

<br>

#### clojure 에서는 자료구조를 시퀀스(seq)라는 하나의 추상(abstraction)으로 다룰 수 있다.

* 시퀀스란? : 논리적 리스트
* 시퀀스는 리스트에 한정되지 않고 어디에서나 사용될 수 있는 추상이다.
* 무엇인가를 모아서 만드는 모든 자료구조는 사실 시퀀스로 취급할 수 있다.
* 시퀀스의 특징 3가지
1) 시퀀스의 첫 번째 원소를 얻어 낼 수 있다.  (first aseq)
2) 첫 번째 원소를 제외한 나머지를 얻어 낼 수 있어야 한다.  (rest aseq)
3) 기존 시퀀스의 앞에 원소를 추가해서 새로운 시퀀스를 만들 수 있다.  consing 이라고 한다. (cons elem aseq)

* seq 함수 : 시퀀스로 만들 수 있는 컬렉션을 받아 시퀀스로 변환한 결과를 반환 (seq coll)
* next 함수 : 첫 원소를 제외한 나머지를 가지고 만든 시퀀스를 반환 (next aseq)
==> (next aseq) == (seq (rest aseq))

<br>

* rest나 cons를 벡터에 적용하게 되면 벡터가 아니라 시퀀스가 반환된다.
```clojure?line_number=false
user> (first [1 2 3 4])
1
user> (rest [1 2 3 4])
(2 3 4)
user> (cons 0 '[1 2 3 4])
(0 1 2 3 4)
```
* 맵 역시 키값 쌍이 나열되어 있는 시퀀스로 생각할 수 있다.
```clojure?line_number=false
user> (first {:name "babo" :alias "foo"})
[:name "babo"]
```
* 집합 역시 가능
```
user> (first #{:the :quick :brown :fox})
:fox
user> #{:the :quick :brown :fox}
#{:fox :the :quick :brown}
==> 클로저의 맵과 집합에는 고정적 방문 순서(stable traversal order)라는 특성이 있다.
==> 방문 순서는 언제나 동일하지만 집어넣은 순서대로  원소들이 나열되지는 않는다.
==> sorted-set, sorted-map 을 사용하면 정렬된 형태로 가능.
==> 물론 추가한 순서대로는 아니다.
user> (sorted-map :c 3 :b 4 :a 1)
=>{:a 1, :b 4, :c 3}
user> (sorted-set :the :quick :brown :fox)
#{:brown :fox :quick :the}
```

<br>

* conj : 하나의 원소를 컬렉션에 추가할 때 사용.
* into : 한 컬렉션의 원소들을 다른 컬렉션에 추가 할 때 사용
* 플로저의 시퀀스 라이브러리는 규모가 큰 시퀀스를 다룰때 적합하다. 대부분의 클로저 시퀀스가 꼭 필요해질 때까지 연산을
미루기 때문에 가용 메모리 용량보다 훨씬 큰 시퀀스도 처리할 수 있다.
* 클로저의 시퀀스는 immutable 하다. --> 시퀀스에 병행적으로 접근하는 것이 안전해진다.
```
user> (conj '(1 2 3) :a)
(:a 1 2 3)
user> (into '(1 2 3) '(a b c))
(c b a 1 2 3)
user> (conj [1 2 3] :b)
[1 2 3 :b]
user> (into [2 3 1] [b c a])
CompilerException java.lang.RuntimeException: Unable to resolve symbol: b in this context, compiling:(*cider-repl localhost*:186:7)
user> (into [2 3 1] [:b :c :a])
[2 3 1 :b :c :a]
user>
```

---

#### Sequence library

##### 시퀀스 생성
```
user> (range 1 25 2)
(1 3 5 7 9 11 13 15 17 19 21 23)
user>
user> (repeat 7 2)
(2 2 2 2 2 2 2)
user> (repeat 10 "w")
("w" "w" "w" "w" "w" "w" "w" "w" "w" "w")
user> (repeat 10 'w')
(w' w' w' w' w' w' w' w' w' w')
```
(iterate f x)
iterate로 만들어지는 시퀀스는 x에서 시작, 함수 f를 적용해 다음 값을 만들어 나가는 일을 무한 반복한다.
(take n sequence)
인자로 받은 sequence의 처음 n개만 반환한다.
```
user> (take 10 (iterate inc 2))
(2 3 4 5 6 7 8 9 10 11)
user>
user> (defn whole-numbers [] (iterate inc 1))
=>#'user/whole-numbers
user> (take 5 (whole-numbers))
(1 2 3 4 5)
```
repeat 은 인자 x를 받아 무한개의 시퀀스를 만든다.
```
user> (take 10 (repeat "x"))
("x" "x" "x" "x" "x" "x" "x" "x" "x" "x")
```
cycle은 컬렉션을 받아 무한히 반복한다.
```
user> (take 4 (cycle '(1 2 3)))
(1 2 3 1)
user> (take 10 (cycle '(1 2 3)))
(1 2 3 1 2 3 1 2 3 1)
```
interleave는 여러 컬렉션을 받아서, 어느 컬렉션의 끝에 도달할 때까지 각 컬렉션의 값이 차례로 교차되는
새로운 컬렉션을 만든다.
```
user> (interleave (whole-numbers) ["aa" "bb" "cc" "dd"])
(1 "aa" 2 "bb" 3 "cc" 4 "dd")
```
interpose는 각 원소 사이에 구분자를 삽입해 새 시퀀스를 만든다.
```
user> (apply str (interpose \. ["aaaa" "bbbb" "ccc"]))
"aaaa.bbbb.ccc"
```

<br>

##### 시퀀스 필터링
filter는 서술식과 컬렉션을 인자로 받아서 컬렉션 가운데 서술식을 만족하는 원소만으로 된 시퀀스를 반환한다.
```
user> (take 10 (filter even? (whole-numbers)))
(2 4 6 8 10 12 14 16 18 20)
```