---
title: 'Python> Python CookBook3판 - O REILLY Study(ch7) function'
layout: post
tags:
  - Language
category: programming
---
#### 7.1 매개변수의 개수 제한, 7.4 여러개의 값을 반환
```python?line_number=false
def arg_test(first, *rest) :
    return (first+sum(rest)), (first + sum(rest)) / (1+len(rest))

# rest는 튜플이다.
# 키워드 매개변수의 제한이 없다.
test_sum, test_average = arg_test(1,2,3,4,5,6,7,8,9,10)
print(test_sum, test_average)

#실제로 튜플을 생성하는 것은 쉼표지 괄호가 아니다.


def anyargs(*args, **kwargs) :
    print(args)     #튜플
    print(kwargs) #딕셔너리

anyargs(1,2,3,{1:'a',2:'b',3:'c'})
```

<br>

#### 7.6 lambda

```python?line_number=false
add = lambda x,y:x+y
print add(2,3)
print add('hello','world')
```

<br>

#### 7.10 callback function
```python?line_number=false
def apply_async(func, args, *, callback) :
    result = func(*args)
    callback(result)

def print_result(result) :
    print('Got :', result)

def add(x,y) :
    return x+y

apply_async(add, (2,3), callback=print_result
```