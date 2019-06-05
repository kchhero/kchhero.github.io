---
title: 'Python> mylab - repr,str'
layout: post
tags:
  - Language
category: programming
---
summary : %r 과 %s의 차이점

### repr(object)
https://docs.python.org/2/library/functions.html#repr

Return a string containing a printable representation of an object. This is the same value yielded by conversions (reverse quotes). It is sometimes useful to be able to access this operation as an ordinary function. For many types, this function makes an attempt to return a string that would yield an object with the same value when passed to eval(), otherwise the representation is a string enclosed in angle brackets that contains the name of the type of the object together with additional information often including the name and address of the object. A class can control what this function returns for its instances by defining a __repr__() method.

<br>

출처 : https://wikidocs.net/6724
자신의 소스코드를 출력하는 코드
```
s = 's = %r;print(s%%s)';print(s%s)
```
--> s = 's = %r;print(s%%s)';print(s%s)

<br>

%r / %s 의 비교 테스트
```
x = "This is test count : %d" % 99
print("Hmm....: %r." % x)
print("Hmm....: %s." % x)
```

<br>

%r ==>
Hmm....: 'This is test count : 99'.

<br>

%s ==>
Hmm....: This is test count : 99.
