---
title: 'static library 추가'
layout: post
tags:
  - Yocto
category: yocto
---
#### Yocto - static library 생성

yocto build시 static library를 생성하는 방법에 대한 memo.
일반적으로 configure 생성시 static library는 default disable 로 setting 되어있다.
따라서 .bbappend 에 아래와 같이 option을 추가해준다.

---

### Work

```
EXTRA_OECONF += "--enable-static \
                                   "
|FILES_${PN}-staticdev = "${libdir}/*.a"
```

그러나 run.do_configure 를 열어보면 oe_runconf() 에 아래와 같이 disable-static option이 바로 따라붙는다.

```
oe_runconf() {
... snip ...
     --enable-static            --disable-static          
... snip ...
}
```

<br>

---

.bbappend 에 아래와 같이 DISABLE_STATIC 을 empty string 을 선언해주어야한다.

<br>

### Work2

```
DISABLE_STATIC = ""
```
