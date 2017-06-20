---
title: programming-clojure-ch2
layout: post
tags:
  - clojure
category: language
---
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

---

#### boolean, nil

* true는 참이고, false는 거싲이다.
* 참과 거짓을 판단하는 상황에서는 nil 역시 false로 평가된다, nil과 false 외에 모든 것은 true로 평가된다.
* 0은 clojure 에서는 거짓이 아니다.
* '서술식(predicate)' 이란, 참과 거짓을 판별하는 함수다.  ex) true?, false?, nil?, zero?

---

#### map, keyword, structure

map은 그 자체가 곧 함수다.
```clojure?line_number=false
user> (def inventors {"Lisp" "McCarthy" "Clojure" "Hickey"})
#'user/inventors
user> (inventors "Lisp")
"McCarthy"
user> (inventors "foo")
nil
```
<br>
get 함수를 사용할 수도 있다.
```clojure?line_number=false
user> (get inventors "Lisp" "Not found!!!")
"McCarthy"
user> (get inventors "foo" "Not found!!!")
"Not found!!!"
user> 
```
<br>
key로 가장 많이 쓰이는 것은 keyword 타입이다. 콜론(:) 으로 시작한다.
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
defstruct 를 구조체를 선언하기 위해 사용한다.
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

이름이 같고 인자 개수가 다른 경우의 함수 정의는 다음과 같이 만들 수 있다.
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

인자 리스트에 &를 사용하면 가변 인자를 받는 함수를 만들 수 있다.
매개변수에 일대일로 바인딩되지 못한, 나머지 인자들의 시퀀스가 & 뒤의 매개변수에 바인딩된다.
가변인자는 재귀 함수를 작성하는 데 매우 유용하다.

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

---

#### var, 바인딩, namespace

<b>var의 초기값을 var의 '루트 바인딩'이라고 한다.</b>
```clojure?line_number=false
user>  (def foo 10)
#'user/foo
user> foo
10
```
리더 매크로를 이용하여 var를 직접 참조
```clojure?line_number=false
user> #'foo
#'user/foo
```

<br>

(let [bindings] exprs)
binding은 exprs 안에서만 효과가 있고, exprs의 마지막 표현식의 값이 let 구문 전체의 값으로 반환된다.
af
<br>

```clojure?line_number=false
user> (defn greet-author-1 [author]
        (println "Hello, " (:first-name author)))
#'user/greet-author-1
user> (greet-author-1 {:last-name "babo" :first-name "foofoo"})
Hello,  foofoo
nil
```
위  코드는 author 전체를 바인딩하고 있다.
==> 

<b>디스트럭처링</b>

```clojure?line_number=false
user> (defn greet-author-2 [{fname :first-name}]
        (println "Hello, " fname))
#'user/greet-author-2
user> (greet-author-2 {:last-name "babo" :first-name "foofoo"})
Hello,  foofoo
nil
```
```clojure?line_number=false
user> (let [[x y] [1 2 3]]
        x y)
2
user> (let [[x y] [1 2 3]]
        [x y])
[1 2]
user> (let [[_ _ z] [1 2 3]]
        z)
3 ; _는 바인딩을 무시한다.
user> (let [[_ _ z] [1 2 3]]
        _)
2 ; 하지만 _ 는 사실 두 번 바인딩된다. 두번째 바인딩된 2가 찍힌다.
```

:as는 컬렉션 전체를 바인딩 한다.
```clojure?line_number=false
user> (let [[x y :as coords] [1 2 3 4 5 6]]
        coords)
[1 2 3 4 5 6]
user> (let [[x y :as coords] [11 21 31 41 51 61]]
        coords)
[11 21 31 41 51 61]
user> (let [[x y :as coords] [11 21 31 41 51 61]]
        (count coords))
6
user> (let [[x y coords] [11 21 31 41 51 61]]
        coords)
31
```

<b>namespace</b>

* 루트 바인딩은 namespace 안에 존재한다.
```clojure?line_number=false
;이름 공간의 확인, resolve
user> (let [x (resolve 'foo)]
        (println "barobaro: " x))
barobaro:  #'user/foo
;이름 공간 만들기
user> (in-ns 'myapp)
#namespace[myapp]
myapp>
```
* 새로운 이름 공간으로 이동했을 때, 클로저의 기본적인 함수를 사용하기 위해서
```clojure?line_number=false
abcd> (clojure.core/use 'clojure.core)
```
* java.lang 외의 클래스 이름은 풀네임으로 기술해야한다. 또는 import 해버린다.
```clojure?line_number=false
abcd> (java.io.File/separator)
"/"
abcd> (import '(java.io InputStream File))
java.io.File
abcd> File/separator
"/"
```

* 다른 이름 공간에 있는 클로저 var를 사용하기 원한다면...
```clojure?line_number=false
abcd> (require 'clojure.contrib.math)
abcd> (clojure.contrib.math/round 1.7)
2
abcd> (round 1.7)
java.lang.Exception:
           ; round를 현재 ns 로 가져오려면..
abcd> (use 'clojure.contrib.math)
nil
           ; 위 코드는 clojure.contrib.math 안에 있는 '모든' public var를 참조한다. round 만 가져오려면..
		   ; ':only'를 사용한다.
abcd> (use '[clojure.contrib.math :only (round)])
nil
abcd> (round 1.2)
2
```

* library 수정 후 사용 프로그램에 변경 내용을 반영하도록 하려면...
```clojure?line_number=false
abcd> (use :reload '[clojure.contrib.math :only (round)])
nil
abcd> (use :reload-all 'examples.exploring)    ; 참조하는 다른 이름 공간까지 모두 리로드 할때.
```

#### 흐름 제어

<b>if 사용 </b>
```clojure?line_number=false
abcd> (defn is-small? [number]
        (if (< number 100) "yes" "no"))
#'abcd/is-small?
abcd> (is-small? 30)
"yes"
abcd> (is-small? 200)
"no"
```
<b>do 부수효과</b>
if는 분기마다 하나의 구문만 오도록 허용한다. 하나 이상의 일을 하고 싶을때 do를 사용하면 여러 구문을 받아 eval 한다.
```clojure?line_number=false
abcd> (defn is-small? [number]
        (if (< number 100)
          "yes"
          (do
            (println "Saw a big number" number)
            "no")))
#'abcd/is-small?
abcd> (is-small? 20)
"yes"
abcd> (is-small? 200)
Saw a big number 200
"no"
abcd>
```
<b>loop/recur 반복</b>
loop는 let과 유사하게, bindings를 설정한 상태에서 exprs를 평가한다. recur을 이용해 반복 가능하게 한다.
```clojure?line_number=false
abcd> (loop [result [] w 5]
        (if (zero? w)
          result
          (recur (conj result w) (dec w))))
[5 4 3 2 1]
abcd>
abcd> (defn countdown [result w]
        (if (zero? w)
          result
          (recur (conj result w) (dec w))))
#'abcd/countdown
abcd> (countdown [] 9)
[9 8 7 6 5 4 3 2 1]
abcd>
    ; 아래는 위와 같은 일을 하는 간단한 방법
abcd> (into [] (take 5 (iterate dec 5)))
[5 4 3 2 1]
abcd> (into [] (drop-last (reverse (range 6))))
[5 4 3 2 1]
abcd> (vec (reverse (rest (range 6))))
[5 4 3 2 1]
```



