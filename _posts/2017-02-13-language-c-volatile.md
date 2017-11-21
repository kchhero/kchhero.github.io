---
title: 'volatile 정리'
layout: post
tags:
  - Language
category: language
---
![2006년 9월호 maso ](http://image.moazine.com/Mid/000119/000000005719.jpg "2006년 9월호 maso ")
이달의 테크 기법｜C-C++ volatile 키워드 / 서광열

#### volatile 소개
volatile로 선언된 변수는 외부적인 요인으로 그 값이 언제든지 바뀔 수 있음을 뜻한다. 따라서 컴파일러는 volatile 선언된 변수에 대해서는 최적화를 수행하지 않는다. volatile 변수를 참조할 경우 레지스터에 로드된 값을 사용하지 않고 매번 메모리를 참조한다. 왜 volatile이라는 키워드가 필요한지 이해하려면 먼저 일반적인 C/C++ 컴파일러가 어떤 종류의 최적화를 수행하는지 알아야 한다. 가상의 하드웨어를 제어하기 위한 다음 코드를 살펴보자.

```c?line_number=false
*(unsigned int *)0x8C0F = 0x8001
*(unsigned int *)0x8C0F = 0x8002;
*(unsigned int *)0x8C0F = 0x8003;
*(unsigned int *)0x8C0F = 0x8004;
*(unsigned int *)0x8C0F = 0x8005;

잘못된 하드웨어 제어 코드
```

이 코드를 보면 5번의 메모리 쓰기가 모두 같은 주소인 0x8C0F에 이루어짐을 알 수 있다. 따라서 이 코드를 수행하고 나면 마지막으로 쓴 값인 0x8005만 해당 주소에 남아있을 것이다. 영리한 컴파일러라면 속도를 향상시키기 위해서 최종적으로 불필요한 메모리 쓰기를 제거하고 마지막 쓰기만 수행할 것이다. 일반적인 코드라면 이런 최적화를 통해 수행 속도 면에서 이득을 보게 된다.
하지만 이 코드가 MMIO(Memmory-mapped I/O)처럼 메모리 주소에 연결된 하드웨어 레지스터에 값을 쓰는 프로그램이라면 이야기가 달라진다. 각각의 쓰기가 하드웨어에 특정 명령을 전달하는 것이므로, 주소가 같다는 이유만으로 중복되는 쓰기 명령을 없애버리면 하드웨어가 오동작하게 될 것이다. 이런 경우 유용하게 사용할 수 있는 키워드가 volatile이다. 변수를 volatile 타입으로 지정하면 앞서 설명한 최적화를 수행하지 않고 모든 메모리 쓰기를 지정한 대로 수행한다.

```c?line_number=false
*(volatile unsigned int *)0x8C0F = 0x8001
*(volatile unsigned int *)0x8C0F = 0x8002;
*(volatile unsigned int *)0x8C0F = 0x8003;
*(volatile unsigned int *)0x8C0F = 0x8004;
*(volatile unsigned int *)0x8C0F = 0x8005;

올바른 하드웨어 제어 코드
```

특정 메모리 주소에서 하드웨어 레지스터 값을 읽어오는 프로그램의 경우도 마찬가지다. 아래 코드의 경우 같은 주소에서 반복적으로 메모리를 읽으므로, 최적화 컴파일러라면 buf[i] = *p;에서 *p를 한 번만 읽어온 후에 그 값을 반복해 사용할 것이다. 하지만 volatile 키워드가 있다면 *p를 참조할 때마다 직접 메모리에서 새 값을 가져와야 한다. 이 경우는 하드웨어가 메모리 0x8C0F 번지 값을 새롭게 변경해 주는 경우이다.

```c?line_number=false
void foo(char *buf, int size)
{
     int i;
     volatile char *p = (volatile char *)0x8C0F;

     for (i = 0 ; i < size; i++)
     {
         buf[i] = *p;
         ...
     }
}
하드웨어 레지스터 읽기
```

---

#### 가시성

volatile 키워드는 앞서 살펴본 하드웨어 제어를 포함하여 크게 3가지 경우에 흔히 사용된다.

1. MMIO(Memory-mapped I/O)
2. 인터럽트 서비스 루틴(Interrupt Service Routine)의 사용
3. 멀티 쓰레드 환경

세 가지 모두 공통점은 현재 프로그램의 수행 흐름과 상관없이 외부 요인이 변수 값을 변경할 수 있다는 점이다. 인터럽트 서비스 루틴이나 멀티 쓰레드 프로그램의 경우 일반적으로 스택에 할당하는 지역 변수는 공유하지 않으므로, 서로 공유되는 전역 변수의 경우에만 필요에 따라 volatile을 사용하면 된다.

```c?line_number=false
int done = FALSE;

void main()
{
     ...
     while (!done)
     {
         // Wait
     }
     ...
}
 
interrupt void serial_isr(void)
{
     ...
     if (ETX == rxChar)
     {
         done = TRUE;
     }
     ...
}
```
serial.c

위 시리얼 통신 예제는 전역 변수로 done을 선언해서 시리얼 통신 종료를 알리는 ETX 문자를 받으면 main 프로그램을 종료시킨다. 문제는 done이 volatile이 아니므로 main 프로그램은 while(!done)을 수행할 때 매번 메모리에서 done을 새로 읽어오지 않는다는 점이다. 따라서 serial_isr() 루틴이 done 플래그를 수정하더라도 main은 이를 모른 채 계속 루프를 돌고 있을 수 있다. done을 volatile로 선언해주면 매번 메모리에서 변수 값을 새로 읽어오므로 이 문제가 해결된다.
인터럽트의 경우와 마찬가지로 멀티 쓰레드 프로그램도 수행 도중에 다른 쓰레드가 전역 변수 값을 임의로 변경할 수 있다. 하지만 컴파일러가 코드를 생성할 때는 다른 쓰레드의 존재 여부를 모르므로 변수 값이 변경되지 않았다면 매번 새롭게 메모리에서 값을 읽어오지 않는다. 따라서 여러 쓰레드가 공유하는 전역 변수라면 volatile로 선언해주거나 명시적으로 락(lock)을 잡아야 한다.
이처럼 레지스터를 재사용하지 않고 반드시 메모리를 참조할 경우 가시성(visibility)이 보장된다고 말한다. 멀티쓰레드 프로그램이라면 한 쓰레드가 메모리에 쓴 내용이 다른 쓰레드에 보인다는 것을 의미한다.

문법과 타입

C/C++의 volatile은 상수(constant)를 선언하는 const와 마찬가지로 타입에 특정 속성을 더해주는 타입 한정자(type qualifier)이다. const int a = 5; 라고 선언했을 경우 a라는 변수는 정수 타입의 변수이면서 동시에 상수의 속성을 가짐을 의미한다. 같은 방식으로 volatile int a; 라고 선언해주면 정수 변수 a는 volatile 속성을 가지게 된다.
조심해야 할 것은 포인터 타입에 volatile을 선언하는 경우이다. int *를 volatile로 선언하는 몇 가지 방법을 비교해보자.

```c?line_number=false
volatile int* foo;
int volatile *foo;
int * volatile foo;
int volatile * volatile foo;
volatile의 선언
```

복잡하게 보이지만 원리는 const 타입 한정자와 동일하다. *를 기준으로 왼쪽에 volatile 키워드가 올 경우 포인터가 가리키는 대상이 volatile함을 의미하고, * 오른쪽에 volatile 키워드가 올 경우에는 포인터 값 자체가 volatile임을 의미한다. volatile이 *의 양쪽에 다 올 경우는 포인터와 포인터가 지시하는 대상이 모두 volatile함을 의미한다. 하드웨어 제어의 예처럼 일반적으로 포인터가 가리키는 대상이 volatile 해야 할 경우가 많으므로 volatile int * 형태가 가장 많이 사용된다. volatile int*와 int volatile*은 동일한 의미이다.

```c?line_number=false
01: int foo(int& a)
02: {
03: }
04:
05: int bar(volatile int& b)
06: {
07: }
08:
09: int main()
10: {
11:     volatile int a = 0;
12:     int b = 0;
13:
14:     foo(a);
15:     bar(b);
16: }
```
```shell?line_number=false
$ g++ a.cc
a.cc: In function `int main()':
a.cc:14: error: invalid initialization of reference of type 'int&' from expression of type 'volatile int'
a.cc:2: error: in passing argument 1 of `int foo(int&)'
```

또한 int는 volatile int의 서브타입에 해당한다. 서브 타입을 쉽게 설명하면 A를 요구하는 곳에 언제든지 B를 사용할 수 있다면 B는 A의 서브 타입이다. 쉬운 예로 Class의 경우 Derived 클래스는 Base 클래스의 서브 타입이다. volatile int를 요구하는 곳에 언제든지 int를 넘길 수 있으므로 int는 volatile int의 서브 타입이라고 말할 수 있는 것이다. 그 예로 위 C++ 프로그램은 컴파일 에러가 난다. volatile int를 받는 bar() 함수에 int 인자로 호출하는 것은 아무 문제없지만, int를 요구하는 foo() 함수에 volatile int 타입인 a를 넘기면 컴파일 에러가 나는 것이다.

```c?line_number=false
01: int foo(int& a)
02: {
03: }
04:
05: int bar(const int& b)
06: {
07: }
08:
09: int main()
10: {
11:     const int a = 0;
12:      int b = 0;
13:
14:     foo(a);
15:      bar(b);
16: }
```
foobar2.cc

이 관계가 쉽게 이해되지 않는다면 위의 예처럼 volatile 키워드를 같은 타입 한정자인 const로 대체해보자. 변수 a는 const int 타입이므로 이미 상수로 선언되었다. const int인 a를 int를 요구하는 foO() 함수에 넘기면 const라는 가정이 깨어지므로 컴파일 에러가 된다. 반대로 b의 경우 원래 const가 아니었지만 bar로 넘기면서 const 속성을 새롭게 부여받게 된다. const 속성을 부여받는다고 말하면 무엇인가 기능이 추가되는 것 같지만, 이는 바꿔 말해서 원래 int의 2가지 기능인 읽기, 쓰기에서 쓰기 기능이 사라지는 것으로 볼 수도 있다. 이를 클래스로 표현해 보면 다음과 같을 것이다.

```c?line_number=false
class ConstInteger {
public:
     ConstInteger(int v) : value(v) {}
     int get() { return value; }

protected:
     int value;
};

class Integer : public ConstInteger {
public:
     Integer(int v) : ConstInteger(v) {}
     void set(int v) { value = v; }
};
```
ConstInteger.cc

위 클래스를 두고 보면 volatile/const int와 int의 관계가 명확해진다. Integer는 ConstInteger을 상속한 클래스이므로 ConstInteger를 요구하는 곳 어디에나 쓸 수 있는 것이다. 반대로 Integer가 필요한 곳에 ConstIntger를 넘기면 set() 메쏘드가 없으므로 문제가 된다. 따라서 컴파일러는 이를 금지하는 것이다.
volatile의 const와 같은 맥락에서 생각할 수 있다. volatile 속성을 부여받는 다는 것은 바꿔 말하면 컴파일러가 최적화를 할 자유를 잃는다고 말할 수 있다. ConstInteger의 경우만큼 명확하지는 않지만 이 관계를 클래스로 생각해 본다면 아마 다음과 같을 것이다.

```c?line_number=false
class VolatileInteger {
public:
     VolatileInteger(int v) : value(v) {}
     int get() { return value; }
     void set(int v) { value = v; }

protected:
     int value;
};

class Integer : public VolatileInteger {
public:
     Integer(int v) : VolatileInteger(v) {}
     void optimize();
};
```
VolatileInteger.cc

---

#### 재배치(reordering)

지금까지 volatile 키워드의 일반적인 기능과 문법에 대해서 살펴보았다. C 표준은 volatile 키워드와 메모리 모델에 대한 명확한 정의를 내리지 않고 있기 때문에 컴파일러마다 그 구현에 다소 차이가 있다. C++ 표준은 volatile 대해 별도의 정의하지 않고 가능한 한 C 표준을 따르라고만 하고 있다.
마이크로소프트의 Visual C++를 예로 들어보면, volatile 키워드에 앞서 살펴본 가시성(visibility) 뿐만 아니라 재배치(reordering) 문제에 대한 해결책도 추가하였다. Visual C++의 volatile 변수는 다음과 같은 기능을 추가로 한다.

1. volatile write: volatile 변수에 쓰기를 수행할 경우, 프로그램 바이너리 상 해당 쓰기보다 앞선 메모리 접근은 모두 먼저 처리되어야 한다.
2. volatile read: volatile 변수에 읽기를 수행할 경우, 프로그램 바이너리 상 해당 읽기보다 나중에 오는 메모리 접근은 모두 이후에 처리되어야 한다.

재배치(reordering)는 컴파일러가 메모리 접근 속도 향상, 파이프라인(pipeline) 활용 등 최적화를 목적으로 제한된 범위 내에서 프로그램 명령의 위치를 바꾸는 것을 말한다. 우리가 프로그램에 a = 1; b = 1; c = 1; 이라고 지정했다고 해서 컴파일된 바이너리가 반드시 a, b, c 순서로 메모리를 쓰지 않을 수 있다는 뜻이다. 만약 a, c가 같은 캐시(cache)에 있거나 인접해 있어서 같이 쓸 경우 속도 향상을 볼 수 있다면 a = 1; c = 1; b = 1; 로 순서가 바뀔 수 있는 것이다.
Visual C++의 경우 volatile을 사용하면 컴파일러가 수행하는 이러한 재배치에 제약을 주게 된다. a = 1; b = 1; c = 1;에서 c가 volatile로 선언된 변수였다면 a = 1;과 b=1;은 반드시 c에 1을 대입하기 전에 일어나야 한다. 물론 a와 b 사이에는 순서가 없으므로 b = 1; a = 1; c = 1; 과 같은 형태로 재배치가 일어날 수는 있다. 재배치가 일어나지 않도록 보장하는 문제가 왜 중요한지는 MSDN에서 발췌한 다음 예를 통해 살펴보자.

```c?line_number=true
#include <iostream>
#include <windows.h>
using namespace std;

volatile bool Sentinel = true;
int CriticalData = 0;

unsigned ThreadFunc1( void* pArguments ) {
	while (Sentinel)
	Sleep(0); // volatile spin lock

	// CriticalData load guaranteed after every load of Sentinel
	cout << "Critical Data = " << CriticalData << endl;
	return 0;
}

unsigned ThreadFunc2( void* pArguments ) {
	Sleep(2000);
	CriticalData++; // guaranteed to occur before write to Sentinel
	Sentinel = false; // exit critical section
	return 0;
}

int main() {
	HANDLE hThread1, hThread2;
	DWORD retCode;

	hThread1 = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)&ThreadFunc1,
	NULL, 0, NULL);
	hThread2 = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)&ThreadFunc2,
	NULL, 0, NULL);

	retCode = WaitForSingleObject(hThread1,3000);

	CloseHandle(hThread1);
	CloseHandle(hThread2);

	if (retCode == WAIT_OBJECT_0 && CriticalData == 1 )
		cout << "Success" << endl;
	else
		cout << "Failure" << endl;
}
```
volatile.cpp

프로그램 수행은 간단하다. 이 프로그램은 쓰레드를 2개 생성하는데 ThreadFunc1은 Sentinel 플래그가 true인 동안 루프를 돌고, ThreadFunc2는 잠시 기다렸다 Sentinel 플래그를 false로 만들어준다. ThreadFunc2는 Sentinel을 false로 만들기 전에 전역 변수인 CriticalData을 1만큼 증가시킨다..
이 프로그램에서 만약 Sentinel이 volatile로 선언되지 않았다면 ThreadFunc1은 가시성을 보장받지 못하므로, ThreadFunc2가 Sentinel의 값을 바꾸더라도, 레지스터에 든 값을 사용해 영원히 루프를 돌 수 있음은 이미 살펴보았다. 그럼 이번에는 volatile로 선언해서 Sentinel의 가시성이 보장된다면 이 프로그램의 수행 결과는 어떻게 될까?
간단히 생각하면 CriticalData를 1증가 시킨 이후에 Sentinel을 false로 바꾸므로 ThreadFunc1은 1이라는 값을 찍을 것이라고 생각할 것이다. 하지만 CriticalData는 volatile이 아니므로 여전히 메모리가 아닌 ThreadFunc2의 레지스터에만 남아있을 확률이 있다. 이 경우 ThreadFunc1은 변경된 CriticalData가 아닌 0이라는 값이 나올 수 있다. 수행 타이밍에 따라서 0이 되기도 1이 되기도 하는 것이다.
가정을 바꿔서 CriticalData 또한 volatile이라고 해보자. 모든 문제가 해결된 것 같지만, 결과는 여전히 0 혹은 1이 나온다. CriticalData가 volatile이면 레지스터가 아닌 메모리에 직접 쓰므로 가시성은 확보되지만, 재배치의 문제가 남아있다. 컴파일러가 보기에 ThreadFunc2의 CriticalData++과 Sentinel = false는 전혀 관계없는 변수이다. 따라서 최적화를 이유로 이 순서를 뒤집어 Sentinel = false를 먼저 수행하고 CiriticalData=+을 수행할 수 있다. 이 경우 ThreadFunc2에서 Sentinel = false만 수행하고 컨텍스트 스위치(context switch)가 일어난 경우 ThreadFunc1은 아직 CriticalData++이 수행되기 전에 CriticalData 값인 0을 읽을 수 있다.
여기서 Visual C++가 추가한 시멘틱(semantic)을 적용해보자. ThreadFunc2에서 Sentinel = false는 volatile write이므로 프로그램 바이너리에서 그 이전에 수행되어야 할 명령은 모두 volatile write 이전에 수행되게 된다. 따라서 CriticalData++;은 반드시 Sentinel = false; 이전에 수행된다. ThreadFunc1은 Sentinel을 volatile read하므로 그 이후에 실행되는 CriticalData 읽기는 반드시 Sentinel을 읽은 후에 수행된다. 따라서 위 프로그램은 정확히 1을 출력하는 올바른 프로그램이 된다.
또한 재배치가 일어나지 않음을 보장할 경우 Sentinel이 volatile이기만 하면 CriticalData는 volatile이 아니더라도 가시성(visibility)이 보장되는 효과도 있다. 이렇게 다른 volatile 변수로 인해 공짜로 가시성을 얻는 경우 피기백킹(piggybacking, 돼지 등을 타고 공짜로 달린다는 의미)이라고 부른다.

좋은 코딩 습관으로 생각되었다가 재배치 문제로 안전하지 않음이 밝혀진 예로 더블 체크 이디엄(double check idiom)이 있다. 아래 코드처럼 initialized == false로 초기화 여부를 확인하고 객체를 생성해 얻어올 경우 반드시 락을 잡아줘야 한다. read-test-write는 원자적(atomic)이지 않기 때문에 여러 쓰레드가 동시에 초기화를 시작할 수 있기 때문이다. 문제는 한 번 초기화가 된 이후에도 매번 객체를 얻어갈 때마다 락을 잡고 풀어야 한다는 점이다.

```c?line_number=false
Foo* Foo::getInstance()
{
mutex.lock();
 
if (instance == 0) {
     instance = new Foo();
}
 
mutex.unlock();
 
return instance;
}
```
check.cc

이러한 오버헤드를 피하기 위해 다음과 같은 일단 초기화 여부를 확인한 이후에 실제로 락을 잡아서 다시 한 번 정말 초기화되지 않았는지 확인하는 패턴이 널리 사용되었다. 이 경우는 앞선 예와는 달리 한 번 초기화가 이루어지고 나면 더 이상 락을 잡지 않고 객체를 얻어올 수 있다.
이 코드는 언뜻 보기에 무척 효율적으로 보이지만 재배치와 관련해 큰 문제가 있다. 특히 instance = new Foo(); 의 수행 순서가 문제가 된다. 메모리를 할당 받아 생성자를 호출한 후에 메모리 주소를 instance를 대입한다고 하면 별 문제가 없겠지만, 일부 필드의 초기화 과정과 instance의 포인터 대입의 순서가 컴파일러 재배치로 인해 바뀔 수 있다. 이 경우 아직 일부 필드가 초기화되지 않은 상태에서 instance가 0이 아니게 되므로, 다른 쓰레드가 객체를 얻을 수 있다.

```c?line_number=false
Foo* Foo::getInstance()
{
	if (instance == 0) {
		mutex.lock();

		if (instance == 0) {
			instance = new Foo();
		}

		mutex.unlock();
	}
	return instance;
}
```
double_check.cc

---

#### 정리

지금까지 C/C++의 volatile 키워드의 기본적인 기능과 관련된 문제들을 살펴보았다. volatile은 미묘한 키워드라 잘 알고 쓰면 큰 도움이 될 수 있지만, 또한 여러 가지 문제를 일으키는 근원이 되기도 한다. 특히 명확한 표준이 있는 게 아니므로, 사용하는 자신이 사용하는 C/C++ 컴파일러의 매뉴얼을 꼼꼼히 읽고 volatile을 어떻게 지원하는지 파악하는 게 중요하다.
volatile은 단일 CPU 환경에서 컴파일러 재배치 문제는 해결해주지만, MMU나 멀티CPU에 의한 재배치에 대해서는 완전한 대안을 제공하지 못한다. 또한 변수를 읽은 후에 값을 수정하고 다시 쓰는 read-modify-write를 원자적으로 수행할 수 있게 해주지도 않는다. a += 5; 같은 단순한 명령도 실제로는 a를 메모리에서 읽고 5를 더한 후에 다시 메모리 쓰는 복잡한 연산이므로 a를 volatile로 선언하는 것만으로는 이 코드를 멀티쓰레드에서 안전하게 수행할 수는 없다는 뜻이다. 유용성과 한계를 충분히 인지하고 필요에 따라 적절히 volatile을 사용하자.
