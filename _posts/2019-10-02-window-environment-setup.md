---
categories: Uncategoried
category: Uncategoried
layout: post
tags:
  - windows
title: 'windows environment setup'
---
## windows environment setup (for suker)

### Tools
	쓸만한/괜찮은 tools ==> configuration은 suker GitHub에...
	* FreeCommander XE
	* 스티커 메모
	* ConEmu (Console Emulation program) :  https://conemu.github.io/en/TableOfContents.html


### Visual Studio 2017
#### emacs keyboard setting
	Emacs Emulation을 설치한 뒤 Options > Environment > Keyboard 에서
	Emacs를 선택한다. 
	Visual Studio를 재시작하면 됨.
	https://marketplace.visualstudio.com/items?itemName=JustinClareburtMSFT.EmacsEmulation


### anaconda3, pyQT5
	1. anaconda 설치
		Windows - https://repo.anaconda.com/archive/Anaconda3-5.2.0-Windows-x86_64.exe
	2.  VS code -> update
		https://demun.github.io/vscode-tutorial/
		VS code + git 조합으로 개발하기
	3. module install
		https://code.visualstudio.com/docs/python/python-tutorial
		https://docs.spyder-ide.org/installation.html
	4. pyQT5 designer 설치
		anaconda3 terminal 실행 > pip3 install pyqt5
		designer 실행 : C:\Users\suker\AppData\Local\Continuum\anaconda3\Library\bin\ Designer.exe 실행
	5. .ui -> .py convert
		pyuic5 -x test.ui -o test.py

<br>

### pyinstaller
	1. pip install pyinstaller
	2. pyinstaller -w -F main.py
	*. RecursionError: maximum recursion depth exceeded 발생시
	   --> xxxxx.spec 에서 아래 코드 추가
	   --> import sys
	   --> sys.setrecursionlimit(5000)
	*. utf-f 관련 error 발생시
	   --> .../pyinstaller/compat.py 에서 
	   -->  out = out.decode(encoding, errors='ignore') 로 수정