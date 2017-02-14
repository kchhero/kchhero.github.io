---
title: programming-clojure
layout: post
tags:
  - clojure
category: language
---
##### 특수변수

- REPL은 최근에 입력된 표현식의 결과 값 세 개를 각각 \*1, \*2, \*3 이라는 특수 변수에  저장하고 있다.
- \*e 는 바로 직전의 예외를 저장하고 있다.

```lisp
user ==> (.printStackTrace *e)
```

---

##### file load
```lisp
;  abc.clj
user ==> (load-file *abc.clj)
```

---

##### 구문
| 구문 | 예 |
| ------------- |
| 키워드 | :tag, :doc|
| 맵(map) | {:name "a", :age 10} |
| 집합 | #{:snap :cracle :pop} |
|symbol | user/foo, java.lang.String |
| vector | [1 2 3] |

---

##### Basic
- 몫과  나머지
```lisp
(quot 22 7)
==> 3
(rem 22 7)
==> 1
```
- 자릿수에 상관없이 정확한 계산을 위해서 -> BigDecimal -> BigDecimal 연산은 자바의 부동소수점
연산에 비해 매우 느리다.
```lisp
(+ 1 (/0.00001 10000000000))
==> 1.0
(+ 1 (/0.00001M 10000000000))
==> 1.000000000000001M
```

- 클로저의 문자(character)는 곧 자바 문자다.
```lisp
user> (str \a \b \c \space \d \e \f)
==> "abc def"
```

