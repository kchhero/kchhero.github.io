---
category: Tools
layout: post
tags:
  - emacs
<<<<<<< HEAD
category: Tools
last_updated: July 3, 2016
=======
title: 'emacs Command 정리'
>>>>>>> 614a969ae67f220954f44b84741b37dfa7641197
---
Emacs Command 정리

#### tab indent
	M-x tabify: Change spaces to tabs where appropriate.
	M-x untabify: Change all tabs to the correct number of spaces (controlled by tab-width).

#### All select
	M-x : dired-toggle-marks


#### Quick calculate
```
quick 진법변환 : C-x * q 

	 Quick calc mode : 10#255   ==> 10진수 255에 대한 16,8,2진수 표시
	 Quick calc mode : 16#ABCD   ==>  16진수 0xABCD에 대한 16,8,2진수 표시
	 Quick calc mode : 16#90000-10#1024    ==> 16진수 0x90000 - 10진수 1K 계산
	 Result: 589824 - 1024 =>  588800  (16#8FC00, 8#2176000, 2#10001111110000000000)
```
