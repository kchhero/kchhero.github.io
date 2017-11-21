---
category: language
layout: post
tags:
  - Language
title: c Kernel code : do-while(0)
---
출처 : [http://kernelnewbies.org/FAQ/DoWhile0](http://kernelnewbies.org/FAQ/DoWhile0)

	MACRO 사용시 do - while(0) 를 사용하는 이유에 대한 article. 
	번역할 필요없이 코드만 한번씩 들여다봐도 직관적으로 이해할 수 있다.


Why do a lot of #defines in the kernel use do { ... } while(0)?

There are a couple of reasons:

- (from Dave Miller) Empty statements give a warning from the compiler so this is why you see #define FOO do { } while(0).
- (from Dave Miller) It gives you a basic block in which to declare local variables.
- (from Ben Collins) It allows you to use more complex macros in conditional code. Imagine a macro of several lines of code like:

```
#define FOO(x) \
        printf("arg is %s\n", x); \
        do_something_useful(x);
```


-- SNIP --
위 링크 참조
