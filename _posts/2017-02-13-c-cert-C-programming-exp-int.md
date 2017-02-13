---
title: 'CERT C 프로그래밍 정리 (EXP, INT)'
layout: post
tags:
  - c
category: language
---
##### EXP01-C. 포인터로 가리키는 타입의 크기를 결정하기 위해 포인터의 크기를 사용하지 마라
```c
double *d_array;

sizeof(d_array);
sizeof(*d_array);
```
==> 컴파일러에 따라서 sizeof(double)과 sizeof(*double)이 다를 수 있다.

---

##### EXP03-C. 구조체의 크기가 구조체 멤버들 크기의 합이라고 가정하지 마라
```c
struct buffer {
size_t size;
char bufferC[50];
};

struct buffer *buf_c = (struct buffer *)malloc(54);
```
==> 54 가 아니다. padding 이 들어간다.
```c
stcurt buffer *buf_c = (struct buffer *)malloc(sizeof(struct buff));
```

---

##### EXP04-C. 구조체끼리 바이트 단위로 비교하지 마라
```
    <bad case>
struct my_buf {
...
};
 
...
const struct my_buf *s1;
const struct my_buf *s2;
...
 
memcmp(s1,s2,sizeof(struct my_buf))
...
```
==>  memcmp 로 구조체간의 비교를 수행하는 경우

==> 대부분의 machine 에서 2byte short은 짝수 address에 배치되고, 4byte long의 경우는 4의 배수 address에 배치된다.

==> memcmp 처럼 byte 단위로 비교하지말고, 구조체의 각 필드별로 비교하도록 한다.

---

##### EXP05-C. const 를 cast 로 없애지 마라
```
void remove_spaces(const char *str, size_t slen) {
	char *p = (char *)str;
	...
}
```
==> C99 6.7.3, 컴파일러는 읽기 전용 영역에 volatile이 아닌 const 객체를 놓게 된다. 경우에 따라서는
       해당 객체의 주소가 사용되지 않는 경우 공간 자체를 할당하지 않을 수도 있다.
```
void remove_spaces(char *str, size_t slen) {
```
==> 객체가 상수라면 컴파일러는 쓰기가 금지된 메모리 영역이나 읽기 전용 영역에 공간을 할당 할 것이다.

---

##### EXP08-C. 포인터 연산이 정확하게 수행되고 있는지 보장하라
> ==> 포인터 연산을 수행할 때 포인터에 더해지는 값은 자동적으로 포인터가 가리키는 객체의 타입으로 조정된다.
          이해하지 못하고 사용하면 buffer overflow를 초래할 수 있다.

```c
#include <stdio.h>
#include <stddef.h>

struct big {
	unsigned long u1;
 	unsigned long u2;
 	unsigned long u3;
 	int s4;
 	int s5;
};

int main(void)
{
	size_t skip = offsetof(struct big, u3);
	struct big *s = (struct big *)malloc(sizeof(struct big));
	if (!s) {
		return 0;
	}
	s->u1 = 1;
	s->u2 = 2;
	s->u3 = 3;
	s->s4 = 4;
	s->s5 = 5;

	printf("%d %d %d %d %d \n", s->u1, s->u2, s->u3, s->s4, s->s5);

	memset(s+skip, 0, sizeof(struct big)-skip);     ==> s addr에 skip 8이 더해지는 것이 아니라 s 타입에 4가 곱해진 값으로 계산된다.
	//memset((char*)s+skip, 0, sizeof(struct big)-skip);
	//==> struct big 포인터를 char* 로 캐스팅하면 skip에 1이 곱해진 조정 값이 사용되므로 정확하게 동작한다.

	printf("%d %d %d %d %d \n", s->u1, s->u2, s->u3, s->s4, s->s5);

	printf("s : %d\n", s);
	printf("skip : %d\n", skip);
	printf("sizeof struct big size : %d\n", sizeof(struct big));
	printf("s + skip size : %d\n", sizeof(s + skip));

	free(s);
	return 0;
}
```