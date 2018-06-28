---
title: Kernel(1)
layout: post
tags:
  - linux
category: Uncategoried
---
summery : kernel 관련 기억/요약/link
### Kernel(1)

참고 : http://studyfoss.egloos.com/category/Kernel

---

#### typeof
GCC permits the identification of a type through the reference to a variable. This kind of operation permits a form of what's commonly referred to as generic programming. Similar functionality can be found in many modern programming languages such as C++, Ada, and the Java™ language. Linux uses typeof to build type-dependent operations such as min and max. Listing 1 shows how you can use typeof to build a generic macro (from ./linux/include/linux/kernel.h).
Listing 1. Using typeof to build a generic macro
```
#define min(x, y) ({                \
    typeof(x) _min1 = (x);          \
    typeof(y) _min2 = (y);          \
    (void) (&_min1 == &_min2);      \
    _min1 < _min2 ? _min1 : _min2; })
```

---
