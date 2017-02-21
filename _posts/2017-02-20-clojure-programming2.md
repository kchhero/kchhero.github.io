---
layout: post
tags:
  - clojure
title: programming-clojure2
category: language
---
#### reader macro

|리더 매크로|예|
|---|---|
|익명 함수|#(.toUpperCase %)
|주석|;한 줄 주석|
|deref|@form --> (deref from)|
|meta|^form --> (meta form)|
|메타데이터|#^metadata form|
|인용|'form --> (quote form)|
|정규식 패턴|#"foo" --> a java.util.regex.Pattern|
|구문 따옴표|`x|
|평가 기호|~|
|이음 평가 기호|~@|
|var-quote|#'x --> (var x)|

---

#### 함수
인자 리스트에 &를 사용하면 가변 인자를 받는 함수를 만들 수 있다.
매개변수에 일대일로 바인딩되지 못한, 나머지 인자들의 시퀀스가 & 뒤의 매개변수에 바인딩된다.

```clojure?line_number=false
user> (defn date [p1 p2 & others]
             (println p1 "and" p2 "gogo with" (count others) "zzz"))
#'user/date
user> (date "Jim" "Me" "Any")
   Jim and Me gogo with 1 zzz
```

---

#### 익명함수
```clojure?line_number=false
user> (defn make-greeter [greeting-prefix]
               (fn [username] (str greeting-prefix ", " username)))
#'user/make-greeter
user> (defn hello-greeter (make-greeter "hello"))
IllegalArgumentException Parameter declaration "make-greeter" should be a vector  clojure.core/assert-valid-fdecl (core.clj:7196)
user> (def hello-greeter (make-greeter "hello"))
#'user/hello-greeter
user> (hello-greeter "world")
"hello, world"
user>
user>
user> ((make-greeter "Aloha") "world")
   "Aloha, world"
user>
```
