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

* $ Step1 select : project위치 설정 ==> /home/suker/docygenPrj<br>
* $ project name & version 설정<br>
* $ source directory select & Scan recursively<br>
* $ Destination dir ==> /home/suker/docygenPrj

<br>

Topics > Mode<br>
* All Entities check<br>
* Include cross-referenced check<br>
* Optimize  for C or PHP output check<br>

<br>

Topics > Output<br>
* HTML > with navigation panel check

<br>

* Topics > Diagrams<br>
* Use dot  tool from the GraphViz package<br>
* Dot graphs to generate All check

<br>

* Expert tab 이동<br>
* Build<br>
* EXTRACT_PRIVATE & EXTRACT_STATIC check<br>
* Source Browser<br>
* INLINE_SOURCES check<br>
* Dot<br>
* CLASS_DIAGRAMS & UML_LOOK check<br>
* DOT_PATH 설정 ==> /usr/bin/dot

<br>

* Run tab 이동<br>
* Run doxygen 실행