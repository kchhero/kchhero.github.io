---
category: language
layout: post
tags:
  - clojure
title: programming-clojure-ch4
---
## data를 sequence로 다루기

<br>

### clojure 에서는 자료구조를 시퀀스(seq)라는 하나의 추상(abstraction)으로 다룰 수 있다.

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

### Sequence library

#### 시퀀스 생성
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

#### 시퀀스 필터링
filter는 서술식과 컬렉션을 인자로 받아서 컬렉션 가운데 서술식을 만족하는 원소만으로 된 시퀀스를 반환한다.
```
user> (take 10 (filter even? (whole-numbers)))
(2 4 6 8 10 12 14 16 18 20)
```
take-while 은 서술식을 컬렉션의 각 원소에 적용해서 거짓이 나타나기 전까지의 원소로만 이루어진 시퀀스를 반환한다.
이후의 컬렉션은 버려진다. 
drop-while 은 take-while의 반대로 동작한다.
```
user> (take-while (complement #{\a\e\i\o\u}) "the-quick-brown-fox")
(\t \h)
user> (drop-while (complement #{\a\e\i\o\u}) "the-quick-brown-fox")
(\e \- \q \u \i \c \k \- \b \r \o \w \n \- \f \o \x)
user>
```
split-at 과 split-with 는 컬렉션을 분할한다.
```
user> (split-at 4 (range 10))
[(0 1 2 3) (4 5 6 7 8 9)]
user> (split-with #(<= % 10) (range 0 20 2))
[(0 2 4 6 8 10) (12 14 16 18)]
```

<br>

#### 시퀀스 서술식
필터링 함수는 서술식을 받아 시퀀스를 반환한다.
시퀀스 서술식은 다른 서술식을 받아 그 서술식을 시퀀스에 어떤 식으로 적용할지를 결정한다.
예를 들어 every?라는 서술식은 인자로 받은 서술식을 시퀀스의 각 원소에 적용한 결과가 모두 참일 때만 참을 반환한다.
```
user> (every? odd? [1 2 3])
false
user> (every? odd? [1 9 3])
true
```
some은 약간 느슨하게 적용한다. 모두 거짓일때만 nil을 반환한다.
```
user> (some even? [11 2 3])
true
user> (some even? [11 5 3])
nil
user> (some identity [nil false 2 nil 5])
2
```

<br>

#### 시퀀스 변환
map은 컬렉션 coll과 함수 f를 받아 coll의 각 원소에 f를 적용한 결과로 만들어진 시퀀스를 반환한다.
(map f coll)
```
user> (map #(format "<p>%s</p>" %) ["the" "quick" "brown" "fox"])
("<p>the</p>" "<p>quick</p>" "<p>brown</p>" "<p>fox</p>")
user> (map #(format "<%s>%s</%s>" %1 %2 %1)
           ["h1" "h2" "h3" "h1"] ["the" "quick" "brown" "fox"])
("<h1>the</h1>" "<h2>quick</h2>" "<h3>brown</h3>" "<h1>fox</h1>")
```
reduce는 coll의 첫 두 원소에 f를 적용한 후, 그 결과와 coll의 세 번째 원소에 다시 f를 적용하고, 이것을 반복한다.
(reduce f coll)
```
user> (reduce + (range 1 11))
55
user> (reduce * (range 1 11))
3628800
```
sort나 sort-by를 이용하면 컬렉션을 정렬할 수 있다.
sort는 원소를 순서대로 정렬
sort-by는 컬렉션의 모든 원소에 a-fn을 적용한 결과를 가지고 정렬
(sort comp? coll)
(soft-by a-fn comp? coll)
```
user> (sort [42 1 7 11])
(1 7 11 42)
```

<br>

리스트 해석(list comprehension)은 조건 제시법을 이용해서 기존 리스트에서 새로운 리스트를 만들어 내는 방법이다.
클로저는 리스트 해석의 개념을 '시퀀스' 해석으로 일반화한다.
클로저의 시퀀스 해석은 매크로를 이용해 이루어진다.
```
(for [binding-form coll-expr filter-expr? ...] expr)
```
for는 binding-form, coll-expr, filter-expr로 이루어진 벡터를 받아 expr에 해당하는 시퀀스를 만들어낸다.
위의 map 의 예제 코드를 리스트 해석을 이용한 코드로 바꾸면...
```
user> (for [word ["the" "quick" "brown" "fox"]]
        (format "<p>%s</p>" word))
("<p>the</p>" "<p>quick</p>" "<p>brown</p>" "<p>fox</p>")
==> 시퀀스에 있는 단어(word)마다(for) 다음 형식(format)을 적용하라.
```
:while은 뒤에 오는 표현이 참으로 유지되는 동안에만 for를 평가한다.
```
user> (for [n (whole-numbers) :while (odd? n)] n)
(1)
```

for의 진정한 강력함은 하나 이상의 binding-form을 사용할때이다.
```
user> (for [file "ABCDEFGH" rank (range 1 9)] (format "%c%d" file rank))
("A1" "A2" "A3" "A4" "A5" "A6" "A7" "A8" "B1" "B2" "B3" "B4" "B5" "B6" "B7" "B8" "C1" "C2" "C3" "C4" "C5" "C6" "C7" "C8" "D1" "D2" "D3" "D4" "D5" "D6" "D\
7" "D8" "E1" "E2" "E3" "E4" "E5" "E6" "E7" "E8" "F1" "F2" "F3" "F4" "F5" "F6" "F7" "F8" "G1" "G2" "G3" "G4" "G5" "G6" "G7" "G8" "H1" "H2" "H3" "H4" "H5" \
"H6" "H7" "H8")
==>바인딩 표현의 오른쪽에 있는 시퀀스의 원소부터 차례로 바인딩된다.
````

<br>

<b>다른 언어들과는 다르게 클로저에서는 대부분의 시퀀스 함수는 그것을 실제로 사용하기전까지는 자신의 일을 
최대한 뒤로 미룬다.</b>

---

### 지연 시퀀스와 무한한 시퀀스

대부분의 클로저 시퀀스는 연산을 '지연'한다. lazy sequence
* 메모리 용량을 초과하는 거대한 데이터 집합을 다룰 수 있다.
* I/O 역시 필요할 때까지 미뤄 둘 수 있다.
* iterate, primes, map은 지연 시퀀스를 반환한다.

#### 지연된 연산을 실행
x가 실제로 원소를 사용하는 부분이 없기 때문에, 클로저는 원소를 굳이 얻어내려고 하지 않는다.
doall을 사용하여 x를 강제로 평가하도록 할 수 있다.
dorun은 지연 시퀀스의 각 원소를 차례로 방문하되, 이전에 방문한 원소는 메모리에 보존하지 않는다.
```
user> (def x (for [i (range 1 3)] (do (println i) i)))
#'user/x
user> (doall x)
1
2
(1 2)
user> (dorun x)
nil
user> (def x (for [i (range 1 3)] (do (println i) i)))
#'user/x
user> (dorun x)
1
2
nil
user> (doall x)
(1 2)
user> (dorun x)
nil
```

---

### 자바 객체

클로저는 아래의 자바 API들을 추상화하여, 시퀀스로 다룰 수 있게 해준다.
* 컬렉션 API, 정규식, 파일 시스템, XML, 관계형 데이터베이스

#### 자바 컬렉션을 시퀀스로 다루기
```
user> (first (.getBytes "hello"))
104
user> (rest (.getBytes "hello"))
(101 108 108 111)
user> (cons (int \h) (.getBytes "ello"))
(104 101 108 108 111)
```
클로저는 컬렉션을 시퀀스처럼 다루게 해주지만, 자동으로 원래 타입으로 되돌려 주지는 않는다.
```
user> (reverse "hello")
(\o \l \l \e \h)
user> (apply str (reverse "hello"))
"olleh"
```
#### 정규식을 시퀀스로...
```
user> (re-seq #"\w+" "the quick brown fox")
("the" "quick" "brown" "fox")
user> (sort (re-seq #"\w+" "the quick brown fox"))
("brown" "fox" "quick" "the")
user> (drop 2 (re-seq #"\w+" "the quick brown fox"))
("brown" "fox")
user> (map #(.toUpperCase %) (re-seq #"\w+" "the quick brown fox"))
("THE" "QUICK" "BROWN" "FOX")
```
#### 파일 시스템을 시퀀스로...
```
user> (.listFiles (File. "."))
#object["[Ljava.io.File;" 0x571189fb "[Ljava.io.File;@571189fb"]
user> (seq (.listFiles (File. ".")))
(#object[java.io.File 0x5fdbec0c "./demo"] #object[java.io.File 0x1cd93933 "./cider"] #object[java.io.File 0x4cca0613 "./clojure-contrib-1.1.0.zip"] #obj\
ect[java.io.File 0x6f6c852c "./myapp1"] #object[java.io.File 0x6f57af41 "./serpent-talk"] #object[java.io.File 0x3a121339 "./study_start"] #object[java.i\
o.File 0x267470f6 "./hello-world"] #object[java.io.File 0x2dbac4f1 "./emacs-for-clojure-book1"] #object[java.io.File 0x7f454ba0 "./.git"] #object[java.io\
.File 0x556404aa "./lein"] #object[java.io.File 0x50923d30 "./README"] #object[java.io.File 0x2bf4e69f "./emacs-for-clojure-book1.zip"] #object[java.io.F\
ile 0x7e39a00a "./cider-nrepl"])
user>
user> (map #(.getName %) (seq (.listFiles (File. "."))))
("demo" "cider" "clojure-contrib-1.1.0.zip" "myapp1" "serpent-talk" "study_start" "hello-world" "emacs-for-clojure-book1" ".git" "lein" "README" "emacs-f\
or-clojure-book1.zip" "cider-nrepl")
user> (map #(.getName %) (.listFiles (File. ".")))
("demo" "cider" "clojure-contrib-1.1.0.zip" "myapp1" "serpent-talk" "study_start" "hello-world" "emacs-for-clojure-book1" ".git" "lein" "README" "emac\
s-for-clojure-book1.zip" "cider-nrepl")
user> (map #(.getName %) (.listFiles (File. ".")))
("demo" "cider" "clojure-contrib-1.1.0.zip" "myapp1" "serpent-talk" "study_start" "hello-world" "emacs-for-clojure-book1" ".git" "lein" "README" "emacs\
-for-clojure-book1.zip" "cider-nrepl")
; 깊이
user> (count (file-seq (File. ".")))
309
```
최근 변경된 파일만 보고 싶을때
```
user> (defn minutes-to-millis [mins] (* mins 1000 60))
#'user/minutes-to-millis
user> (defn recently-modified? [file]
        (> (.lastModified file)
           (- (System/currentTimeMillis) (minutes-to-millis 30))))
#'user/recently-modified?
user> (filter recently-modified? (file-seq (File. ".")))
```

#### 스트림을 시퀀스로...
line-seq를 이용하면 자바 Reader가 읽어들인 라인들을 시퀀스로 다룰 수 있다.

<br>

#### XML을 시퀀스로...
```
user> (use '[clojure.xml :only (parse)])
user> (parse (java.io.File. "colors.xml"))
{:tag :resources, :attrs nil, :content [{:tag :color, :attrs {:name "control_background"}, :content ["#cc4285f4"]}]}
user> (for [x (xml-seq
               (parse (java.io.File. "colors.xml")))
            :when (= :color (:tag x))]
        (:composer (:attrs x)))
(nil)
```
많은 양의 XML 데이터를 처리해야 한다면, clojure-contrib에 포함된 lazy-xml과 zip-filter/xml 라이브러리를 이용해보자.

---

<br>

### 특정 자료구조용 함수
클로저의 시퀀스 함수는 매우 범용적인 코드를 작성하도록 해주지만 때로는 특정 자료구조에 특화된 함수를 이용하는 것이
좋을 수도 있다.

#### 리스트에 대한 함수
peek은 first와 똑같지만 pop은 rest와 다르다.
```
==> (peek coll)
==> (pop coll)
user> (peek '(1 2 3))
1
user> (rest '(1 2 3))
(2 3)
user> (pop '(1 2 3))
(2 3)
user> (rest ())
()
user> (pop ())
IllegalStateException Can't pop empty list  clojure.lang.PersistentList$EmptyList.pop (PersistentList.java:209)
```

#### 벡터에 대한 함수
```
==> 역시 peek, pop을 지원하지만 벡터의 끝이 기준이 된다는 점이 다르다.
user> (peek [1 2 3])
3
user> (first [1 2 3])
1
user> (pop [1 2 3])
[1 2]
user> (rest [1 2 3])
(2 3)
==> get은 인덱스에 해당하는 값을 반환한다. out of index인 경우는 nil을 반환
user> (get [:a :b :c] 1)
:b
user> (get [:a :b :c] 4)
nil
user> ([:a :b :c] 2)
:c
user> ([:a :b :c] 5)
IndexOutOfBoundsException   clojure.lang.PersistentVector.arrayFor (PersistentVector.java:158)
==> assoc는 특정 인덱스에 새 값을 집어넣는다.
user> (assoc [:a :b :c :d :e] 1 4)
[:a 4 :c :d :e]
==> subvec은 기존 벡터의 일부를 반환한다. end가 명시되지 않으면 벡터의 끝이 end가 된다.
user> (subvec [1 2 3 4 5] 3)
[4 5]
user> (subvec [1 2 3 4 5] 3 4)
[4]
user> (subvec [1 2 3 4 5] 1 3)
[2 3]
==> take와 drop을 사용해도 흉내 낼 수 있다.
user> (take 2 (drop 1 [1 2 3 4 5]))
(2 3)
user>
```
subvec은 벡터에만 사용할 수 있는 대신 실행 속도가 '훨씬' 빠르다.

#### 맵에 대한 함수
keys, vals : key, value 반환
```
user> (keys {:aaa "aaa", :bbb "bbb"})
(:aaa :bbb)
user> (vals {:aaa "aaa", :bbb "bbb"})
("aaa" "bbb")
user>
```
get : key에 대한 값 또는 nil 반환
```
user> (get {:aaa "aaa", :bbb "bbb"} :aaa)
"aaa"
user> (get {:aaa "aaa", :bbb "bbb"} :ccc)
nil
==> get을 굳이 안써도 됨.
user> ({:aaa "aaa", :bbb "bbb"} :bbb)
"bbb"
user> ({:aaa "aaa", :bbb "bbb"} :ddd)
nil
```
key에 해당하는 값을 찾았는데, 값이 nil인지 key가 없는지를 확인하려면..
contains
```
user> (def score {:stu nil :joey 100})
#'user/score
user> (:stu score)
nil
user> (:abc score)
nil
user> (contains? score :abc)
false
user> (contains? score :stu)
true
==> 다른 방법 : get의 세번째 인자로 값이 없을 경우의 value를 넘긴다.
user> (get score :stu :score-not-found)
nil
user> (get score :abc :score-not-found)
:score-not-found
```

<br>

merge는 맵들을 합친다. 여러 맵이 같은 키를 가질 경우, 가장 오른쪽 맵의 키/값이 살아남는다.
merge-with는 merge와 비슷하지만 둘 이상의 맵이 같은 키를 가질 경우, 어떻게 조합해서 키에 대한
값을 만들어 낼지 함수로 지정할 수 있다.

<br>

#### 집합에 대한 함수
clojure.set
* union : 합집합
* intersection : 교집합
* differnce : 차집합
* select : 서술식을 만족시키는 모든 원소의 집합
```
user> (def languages #{"java" "c" "d" "clojure"})
user> (def letters #{"a" "b" "c" "d" "e"})
user> (def beverages #{"java" "chai" "pop"})
user> (use '[clojure.set])
nil
user> (union languages beverages)
#{"d" "clojure" "pop" "java" "chai" "c"}
user> (difference languages beverages)
#{"d" "clojure" "c"}
user> (intersection languages beverages)
#{"java"}
user> (select #(= 1 (.length %)) languages)
#{"d" "c"}
```

관계대수...
