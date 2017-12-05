---
title: 'development - Doxygen'
layout: post
tags:
  - development
category: private
---
#### doxygen 사용법 정리

출처 : http://janghw.tistory.com/entry/doxygen-%EC%82%AC%EC%9A%A9%EB%B2%95

$ doxywizard

<br>

$ Step1 select : project위치 설정 ==> /home/suker/docygenPrj
$ project name & version 설정
$ source directory select & Scan recursively
$ Destination dir ==> /home/suker/docygenPrj

<br>

Topics > Mode
--> All Entities check
--> Include cross-referenced check
--> Optimize  for C or PHP output check

<br>

Topics > Output
--> HTML > with navigation panel check 

<br>

Topics > Diagrams
--> Use dot  tool from the GraphViz package
--> Dot graphs to generate All check

<br>

Expert tab 이동
--> Build
--> EXTRACT_PRIVATE & EXTRACT_STATIC check
-->  Source Browser
--> INLINE_SOURCES check
--> Dot
--> CLASS_DIAGRAMS & UML_LOOK check
--> DOT_PATH 설정 ==> /usr/bin/dot

<br>

Run tab 이동
--> Run doxygen 실행