---
category: language
layout: post
tags:
  - c
title: 'CERT C 프로그래밍 정리 (PRE, DCL)'
---
#### 에이콘, CERT C 프로그래밍, 로버트 C. 시코드.. 공부하면서 중요부분을 정리함.

---

---


##### PRE00-C. 함수형의 매크로보다 인라인이나 정적함수를 사용하라
```
#define CUBE(X) ((X)*(X)*(X))
/*…*/
int i=2;
int a=81 / CUBE(++i);
```
==> 다음과 같이 치환된다.
```
int a=81 / ((++i)*(++i)*(++i));
```
매크로 정의를 인라인 함수로 바꾼다.
```
inline int cube(int i) {
	return i*i*i;
}
```

---
```
size_t count=0;

#define EEE(ff) (ff(), ++count)
void g(void) {
     printf("called g, count = %zu.\n", count);
}
void aF(void) {
    size_t count=0;
     while (count++<10)    {
         EEE(g);
     }
}
```
==> EEE 매크로를 사용해 함수를 호출하고 전역 카운터를 증가시킨다. 코드에서 매크로 치환이 함수 구현부에서 일어나기 때문에
매크로의 인자를 함수 내부 스코프에서 찾는다.
```
#define EEE(ff) (ff(), ++count)
==>
typedef void (*EF)(void);
inline void EEE(EF f) {
     f();
     ++count;
}
```
==> 인라인 함수는 구현부가 컴파일될때 식별자를 전역 변수로 처리하기 때문에 함수가 호출될때 앞의 경우처럼 동일한
이름의 다른 변수로 인식 할 위험이 없다.

---

****인라인함수를호출한 함수내에서 벗어나지않고 값을 치환하여 작동한다. 반대로 함수는 함수호출시 기존함수에서벋어나 함수정의한 부분으로 넘어가 처리한다. ****

1. 인라인 함수로는 구현할 수 없는 형태의 연산을 만들기 위해 매크로를 사용할 수 있다. 아래의 경우
```
#define SELECT(s,v1,v2) ((s) ? (v1):(v2))
```

2. 컴파일시 매크로에서는 상수가 되는 표현식이 인라인 함수에서는 표현식이 된다.
```
#define ADD_M(a,b) (a+b)
inline int add_f(int a,int b) {
    return a+b;
}
```
3. 함수는 값에 의한 호출, 매크로는 이름에 의한 호출

---

##### PRE03-C.타입 인코딩시 매크로 정의 대신 타입 정의를 사용하라.
typedef는 스코프 규칙이 적용되는 반면 매크로 정의는 적용되지 않는다.
typedef는 단순한 텍스트 치환이 아니기 때문에 포인터 타입을 정확하게 인코딩 할 수 있다.
```
#define cstring char *
cstring s1, s2;

void main()
{
	cstring s1,s2;
	s1="aaaa";
	s2="bbbb";
	printf("%s, %s",s1,s2);
}
```
==>결과 : Segmentation fault (core dumped)
==> s1이 char * 로 선언되었지만 s2는 의도와는 달리 char로 정의 되었다.
```
typedef char * cstring;
cstring s1, s2;
```
==>결과 : aaaa, bbbb

---

##### PRE05-C. 토큰들을 연결하거나 문자열 변환을 할 때 매크로 치환을 고려하라.
매크로에서 ## 이나 # 사용시 주의할 점.
```
#define str(s) #s
#define foo 4

void main(void) {
	printf("test3 : %s\n",str(foo));
}
```
==>결과 : test3 : foo

```
#define xstr(s) str(s) 를 추가하여 두 단계의 매크로를 이용해야한다. 매크로의 치환 순서에 따라서 실제 값으로 치환 후 전달된다.
...
printf("test3 : %s\n",xstr(foo));
...
```
==>결과 : test3 : 4

---

##### PRE08-C. 헤더 파일 이름에 대한 주의 사항 (ISO/IEC 9899:1999] Section 6.10.2 "Source File Inclusion"
- 처음 여덟 글자만 헤더 파일 이름으로 보장할 수 있다.
- 파일 이름의 점 뒤에는 숫자가 아닌 문자 한 개만 쓸 수 있다.
- 파일 이름에서 대소문자 구별은 보장하지 않는다.

---

##### PRE10-C. do-while 루프 사용
복수 구문의 매크로는 do{...}while(0) 루프로 감싸는 방법을 사용한다.

---

##### DCL00-C. 변하지 않는 객체는 const로 보장해둬라
const 는 중독성이 있고, 코드의 관리 부담을 증가시킨다. 객체를 const로 선언하는 대신 매크로나 열거형 상수를 사용할 수 있다.
DCL06-C 참고, 변수를 열거형 상수나 매크로로 치환하기 전에 먼저 변수에 const를 붙여보는 것은 좋은 시도다. compile시 warning 메시지를 보여주기 때문이다. const로 변수가 중간에 바뀌지 않음을 확인했다면 설계에 맞게 열거형 상수나 매크로로 바꿀 수 있다.

---

##### DCL03-C. 상수 수식의 값을 테스트할 때 정적 assertion을 사용하라
assert() 매크로는 부정확한 가정을 판별하기에는 쓸 만하나 런타임 에러를 체크하기에는 부적절하기에 서버 프로그램이나 임베디드 프로그램에서는 일반적으로 사용하지 않는다.
아직 C99 표준은 아님
```
#define JOIN(x,y) JOIN_AGAIN(x,y)
#define JOIN_AGAIN(x,y) x##y

#define static_assert(e) \
  typedef char JOIN(assertion_failed_at_line, __LINE__) [(e) ? 1:-1]

struct timer {
  int MODE;
  int DATA;
  int COUNT;
};

void main(void)  {
	static_assert(offsetof(struct timer, DATA) == 4);
}
```
==>e가 0이 아니면 원가가 하나인 배열이 선언된다. 반대의 경우는 에러를 발생하면서 컴파일러의 의해 걸러진다.

---

##### DCL04-C. 한 번에 여러 변수를 선언하지 마라.
한번에 한개만 한줄에. 변수의 역할을 설명한 주석.

char *src=0, c=0    ==> src는 char* 타입이고, c는 char 타입이다.
char* p, q;            ==> 혼란스러운/어리석은 코드
int i, j=1;              ==> j만 초기화됐고, i는 초기화되지 않음.

** 예외 : for loop 문 내부의 루프 카운터와 같은 경우는 예외.

---

##### DCL06-C. 심볼릭 상수의 사용
const : 특정 스코프 내에서 사용할 수 있고, 컴파일러가 타입체크를 해주며, 매크로와는 달리 디버깅시 심볼 정보가 보인다. 대신 메모리를 사용한다.
아래의 경우는 사용할 수 없다.
- 구조체 내부의 비트 단위로 정의된 멤버의 크기, 배열의 크기, 열거형 상수의 값, case 상수의 값.

```
const int max = 15;
int a[max];  //==>  함수 밖에서 일어난 부적절한 선언
const int *p;
p = &max;  //==> const 지정 객체의 address를 사용할 수 있다.

int value;
const int signalmin = some_function();
switch(value)
{
   case signalmin:
   break;
}
```
==> 이경우 "error: case label does not reduce to an integer constant" 이런 error message를 만나게 된다.
  const는 런타임에 evaluated 되는데, swtich의 label의 compile time에 evaluated 된다.


- 열거형 상수 : 값의 타입을 정의 할 수 없다. 값은 항상 int다

| 방식 | 평가시점 | 메모리 소비 | 디버깅 심볼 | 타입 체크 | 컴파일시 상수 표현식 |
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------ |
| 열거형 | 컴파일 타임 | X | O | O | O |
| const | 런타임 | O | O | O | X |
| 매크로 | 전처리기 | X | X | X | O |

---

##### DCL10-C. 기변 인자를 가진 함수의 사용
```
#include <stdarg.h>

enum {e_vaol = -1};

unsigned int average(int first, ...)
{
	unsigned int count = 0;
	unsigned int sum = 0;
	int i =  first;
	va_list args;

	va_start(args, first);

	while (i != e_vaol) {
		sum += i;
		count++;
		i = va_arg(args, int);
	}
	va_end(args);
	return (count ? (sum/count) : 0);
}

void main(void)
{
	unsigned int avg = average(0,6,va_eol);
	printf("average : %d",avg);
}
```
==> va_eol을 빼먹으면 segmentation fault error를 만나게 된다.

---

##### DCL13-C. 함수에 의해 바뀌지 않을 값에 대한 포인터를 함수의 매개변수로 사용할 때는 const로 정의하라.
	==>  함수의 매개변수를 const로 선언하는 것은 함수가 그 값을 바꾸지 않는다고 약속하는 것과 같다. C에서 함수의 인자는 참조가 아닌
             값에 의해 전달된다. 함수가 받은 인자를 바꾼다더라도 반환될때는 무효화된다. 포인터 역시 비슷하다.
```
void foo(int x) {
	x = 3; /*함수 밖으로 나가기 전까지만 유효*/
}

void foo(int *x) {
	if (x != NULL) {
		*x = 3; /*함수 밖에서도 적용됨*/
	}
}
```
==> 포인터로 참조되는 값은 바뀌고, 컴파일러가 걸러내지 못한다.
```
void foo(const int *x) {
	if (x != NULL) {
		*x = 3;  /* 컴파일 에러 발생*/
	}
}
```
==> const 사용으로 코드의 가독성과 일관성을 높이는데 도움이 된다.

---

##### DCL15-C. 현재 범위를 넘어서까지 사용되지 않을 객체는 static으로 선언하라.
	==> C99 6.2.2 : 한 파일 스코프에서 객체나 함수에 대한 식별자의 선언이 static을 포함하고 있으면 식별자는 내부에서만 결합(사용)된다.
	==> 외부 링크(linkage)를 갖는, 외부 결합을 갖는 객체를 많이 만들면 naming이 복잡해지거나 library와의 이름 충돌이 발생한다.

---

##### DCL30-C. 객체를 선언할 때 적절한 지속공간을 지정하라.
```
char *init_array(void) {
	char array[10];
	...
	return array;
}
```
==> local stack의 변수에 대한 포인터를 반환하려 하고 있다.
```
void init_array(char array[]) {
	...
	return ;
}
int main(...) {
	char array[10];
	init_array(array);
	...
	return 0;
}
```
==> 함수 밖에서 array를 정의하고 인자로 넘기면 된다.

---

##### DCL34-C. volatile
비동기 시그널 처리와 같은 경우 의도하지 않은 최적화가 일어난다.volatile을 지정하면 컴파일러가 의도하지 않은 재정렬이나 최적화를 수행하지 않도록 보장한다.
```
#include <signal.h>

sig_atomic_t i;               ==> volatile sig_atomic_t i;

void handler(int signum) {
  i = 0;
}

int main(void) {
	i = 1;
	signal(SIGINT,handler);
	while(i) {
		printf("test8\n");
	}
	return 0;
}
```
==> 변수 선언에 volatile을 추가해 i 가 while 루프에 매번 들어갈 때나 시그널 핸들러 내부에서 사용될 때 원래 주소에 접근해 진행하
       도록 보장할 수 있다.