---
title: programming-clojure-ch1
layout: post
tags:
  - clojure
category: language
---
##### BASIC
user==> (defn hello [name] (str "Hello, " name))
* defn : 함수를 정의
* hello : 함수 이름, name이라는 인자를 받는다.
* str : 여러 인자를 받아 하나의 문자열로 연결하는 함수
* 실행 : 
user==> (hello "test")
    Hello, test


몫과  나머지
```lisp
(quot 22 7)
==> 3
(rem 22 7)
==> 1
```
자릿수에 상관없이 정확한 계산을 위해서 -> BigDecimal -> BigDecimal 연산은 자바의 부동소수점
연산에 비해 매우 느리다.
```lisp
(+ 1 (/0.00001 10000000000))
==> 1.0
(+ 1 (/0.00001M 10000000000))
==> 1.000000000000001M
```

클로저의 문자(character)는 곧 자바 문자다.
```lisp
user> (str \a \b \c \space \d \e \f)
==> "abc def"
```

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
user ==> (load-file "abc.clj")
```

---

##### 병행성 예제 - preview
```lisp
(def visitors (ref #{}))

(defn hello
  "Writes hello message to *out*. Calls you by username.
  Knows if you have been here before."
  [username]
  (dosync
   (let [past-visitor (@visitors username)]
     (if past-visitor
       (str "Welcome back, " username)
       (do
         (alter visitors conj username)
         (str "Hello, " username))))))
```
```
user> (load-file "helloworld.clj")
#'user/hello
user> (hello "ASDF")
"Hello, ASDF"
user> (hello "ASDF")
"Welcome back, ASDF"
```

---

##### 라이브러리

* require : clojure library를 load 한다.
```
(require quoted-namespace-symbol)
```
* refer : 현재 namespace 의 모든 이름을 새로운 namespace로 대응 시킬 수 있다.
```
(refer quoted-namespace-symbol)
```
* use : require와 refer가 한꺼번에 이루어진다.
```
use quoted-namespace-symbol)
```
* reload-all : 강제로 library를 다시 load 하도록 할 수 있다.
```
     (use :reload-all 'eamples.introduction)
```

---

##### 문서 찾아보기

* doc
```
user> (doc str)
clojure.core/str
([] [x] [x & ys])
  With no args, returns the empty string. With one arg x, returns
  x.toString().  (str nil) returns the empty string. With more than
  one arg, returns the concatenation of the str values of the args.
nil
user>
```
위의 병행성 예제 4line 이 바로 기술 문서의 역할이다.
```
(doc hello)
```
==> message를 확인 해 볼 수 있다.

* find-doc : function name을 정확하게 알지 못할때 사용한다.

