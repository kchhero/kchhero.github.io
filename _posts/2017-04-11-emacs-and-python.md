---
category: Tools
layout: post
tags:
  - emacs
title: 'emacs 와 python'
---
#### ipython & Emacs
현재 나의 ~/.emacs.d/python-init.el 은 아래처럼 설정 되어있다.

```lisp
(defun use-python3 ()
   "Use the system python3 for `elpy-mode', `flycheck-mode', and `python-mode'."
   (interactive)
   (setq
     elpy-rpc-python-command "/usr/bin/python3.5"
     elpy-rpc-pythonpath "/usr/local/lib/python3.5/dist-packages"
     flycheck-python-flake8-executable "/usr/bin/flake8"
     python-check-command "/usr/bin/pyflakes3"
     python-shell-interpreter "/usr/bin/ipython3"))
(snip)
```

C-c C-c 로 python 소스를 실행시키면 같은 경로에 위치한 다른 .py 의 module을 찾지 못하는 문제가 
계속 발생하고 있다. ImportError: No module named xxxxx

google 을 통해서 검색해보면 아래처럼 현재 경로를 추가하라고 하는데, 별 효과는 없다.
```
improt sys
sys.path.append("./")
```

그런데, 
```
import os
os.getcwd()
```
를 추가하였더니 잘 실행된다....

앞으로 이 부분을 조금 더 분석할 생각이다.

<br>

#### error in process sentinel: peculiar error: "exited abnormally with code 1"
emacs에서 .py 파일 작업시 시스템이 느려지고 위의  메시지가 뜨는 경우가 있다.
M-x : elpy-config 으로 화인.  (terminal --> $ python -m elpy)

elpy를 최신 버전으로 바꾸니(1.14.1 -> 1.17.1) 해결된다. root cause는 아직 모르겠다.
