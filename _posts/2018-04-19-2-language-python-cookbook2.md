---
title: 'Python> Python CookBook3판 - O REILLY Study(ch10) module, packaging'
layout: post
tags:
  - Language
category: programming
---
#### 10.1 Module

```python?line_number=false
Graphics/
	__init__.py      # 계층적으로 구성하는 패캐지로 정리하기 위함.
	primitive/
		__init__.py
		line.py
		fill.py
	formats/
		__init__.py
		png.py
		jpg.py
```

<br>

```python?line_number=false
# graphics/__init__.py가 import 되고, graphics namespace를 형성한다.
import graphics
# __init__.py 에 다음처럼 정의하면 import graphics.formats만 실행해도 된다.
from . import jpg
from . import png
```

<br>
<br>

#### 10.15 배포
```python?line_number=false
#setup.py
from distutils.core import setup

setup (name = 'distTT',
       version = '1.0',
       author = 'suker',
       packages = ['distTTIN', 'distTTIN.utils'],
       )

#MANIFEST.in
include *.txt
recursive_include examples *
recursive_include Doc *

```
```
$ tree
.
├── distTTIN
│   ├── bar.py
│   ├── foo.py
│   ├── __init__.py
│   └── utils
│       ├── grok.py
│       ├── __init__.py
│       └── spam.py
├── Doc
│   └── docu.txt
├── examples
│   └── hello.py
├── MANIFEST
├── MANIFEST.in
├── README.txt
└── setup.py

4 directories, 12 file
```
```
python3 setup.py sdist
```

<br>

##### 결과

```
$ tree
.
├── distTT-1.0
│   ├── distTTIN
│   │   ├── bar.py
│   │   ├── foo.py
│   │   ├── __init__.py
│   │   └── utils
│   │       ├── grok.py
│   │       ├── __init__.py
│   │       └── spam.py
│   ├── PKG-INFO
│   ├── README.txt
│   └── setup.py
└── distTT-1.0.tar.gz

3 directories, 10 files
```
