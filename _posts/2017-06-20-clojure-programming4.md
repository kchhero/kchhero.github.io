---
title: programming-clojure-ch4
layout: post
tags:
  - clojure
category: language
---
### data를 sequence로
<br>

#### 구문

| 구문 | 예 |
| ------------- |
| 키워드 | :tag, :doc|
| 맵(map) | {:name "a", :age 10} |
| 집합 | #{:snap :cracle :pop} |
|symbol | user/foo, java.lang.String |
| vector | [1 2 3] |

---

#### 문자, 문자열

clojure는 자바의 문자열 메서드를 대부분 추상화하지 않는다. 그냥 바로 호출한다.
```clojure?line_number=false
(.toUpperCase "hello")
=>   "HELLO"
#점(.)은 toUpperCase를 clojure 함수가 아니라 자바 메서드로 다루라는 뜻이다.
```
<br>
str은 여러 인자를 받는 함수이다. 아래의 경우 하나의 리스트를 넘겨받는다. 
apply를 사용하면?
```clojure?line_number=false
형식 : (apply f args* argseq)

user> (apply str (interleave "Attack at midnight"
                             "The purple elephant chortled"))
"ATthtea cpku raptl em iedlneipghhatn"
# interleave : 두 메시지의 조합
```
```lisp
user> (apply str (take-nth 2 "ATthtea cpku raptl em iedlneipghhatn"))
"Attack at midnight"
# take-nth : 메시지 시퀀스에서 첫 문자부터 두 칸 단위로 문자를 추출
```



