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

##### file load
```lisp
;  abc.clj
user ==> (load-file *abc.clj)
```