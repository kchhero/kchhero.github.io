---
title: programming-clojure-ch2
layout: post
tags:
  - clojure
category: language
---
### clojure overview

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
* clojure는 자바의 문자열 메서드를 대부분 추상화하지 않는다. 그냥 바로 호출한다.
```clojure?line_number=false
(.toUpperCase "hello")
=>   "HELLO"
#점(.)은 toUpperCase를 clojure 함수가 아니라 자바 메서드로 다루라는 뜻이다.
```

<br>

* str은 여러 인자를 받는 함수이다. 아래의 경우 하나의 리스트를 넘겨받는다.apply를 사용하면?
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

---

#### boolean, nil
* true는 참이고, false는 거싲이다.
* 참과 거짓을 판단하는 상황에서는 nil 역시 false로 평가된다, nil과 false 외에 모든 것은 true로 평가된다.
* 0은 clojure 에서는 거짓이 아니다.
* '서술식(predicate)' 이란, 참과 거짓을 판별하는 함수다.  ex) true?, false?, nil?, zero?

---

#### map, keyword, structure
* map은 그 자체가 곧 함수다.
```clojure?line_number=false
user> (def inventors {"Lisp" "McCarthy" "Clojure" "Hickey"})
#'user/inventors
user> (inventors "Lisp")
"McCarthy"
user> (inventors "foo")
nil
```

<br>

* get 함수를 사용할 수도 있다.
```clojure?line_number=false
user> (get inventors "Lisp" "Not found!!!")
"McCarthy"
user> (get inventors "foo" "Not found!!!")
"Not found!!!"
user> 
```

<br>

* key로 가장 많이 쓰이는 것은 keyword 타입이다. 콜론(:) 으로 시작한다.
```clojure?line_number=false
user> (def inventors {:Lisp "McCarthy" :Clojure "Hickey"})
#'user/inventors
user> (inventors :Lisp)
"McCarthy"
user> (:Lisp inventors)
"McCarthy"
user>
# 키워드 역시 함수다. 키워드는 맵을 인자로 받아 키에 해당하는 값을 반환한다.
```

<br>

* defstruct 를 구조체를 선언하기 위해 사용한다.
```clojure?line_number=false
user> (defstruct book :title :author)  ;defstruct를 이용해 book 구조체를 선언한다
#'user/book
user> (def b (struct book "Anathem" "Neal Stephenson")) ;struct를 이용해 book 구조체의 instance를 만든다.
#'user/b
user> b
{:title "Anathem", :author "Neal Stephenson"}
user> (:title b)
"Anathem"
user> (struct-map book :copyright 2008 :title "Babo")
{:title "Babo", :author nil, :copyright 2008}
;struct-map 함수를 이용하면, 속성 값 중 일부를 빼거나 속성에 없는 key/value 를 추가할 수도 있다.
```

---

#### reader macro

* reader 는 clojure의 구문을 읽어 텍스트를 클로저 자료구조로 변환한다.
* reader macro 란, 표현식 앞에 왔을 때 reader로 하여금 특수한 행동을 하게 만드는 문자다.
* 인용(')부호 문자의 경우 표현식의 값을 평가하지 않는다. 세미콜론(;) 은 주석이다.

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

* 이름이 같고 인자 개수가 다른 경우의 함수 정의는 다음과 같이 만들 수 있다.
```clojure?line_number=false
user> (defn greeting
        "clojure function study"
        ([] (greeting "world" ))
        ([username] (str "Hello, " username)))
#'user/greeting
user> (greeting)
"Hello, world"
user> (greeting "babo")
"Hello, babo"
```

<br>

* 인자 리스트에 &를 사용하면 가변 인자를 받는 함수를 만들 수 있다.
* 매개변수에 일대일로 바인딩되지 못한, 나머지 인자들의 시퀀스가 & 뒤의 매개변수에 바인딩된다.
* 가변인자는 재귀 함수를 작성하는 데 매우 유용하다.
```clojure?line_number=false
user> (defn date [p1 p2 & others]
             (println p1 "and" p2 "gogo with" (count others) "zzz"))
#'user/date
user> (date "Jim" "Me" "Any")
   Jim and Me gogo with 1 zzz
```

---

#### 익명함수

* 함수가 너무 간단한 경우  ==>  필터 함수
* 함수의 내부에서만 쓰이는 경우
* 함수 내부에서 데이터를 이용해 동적으로 함수를 만들어 내는 경우
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
