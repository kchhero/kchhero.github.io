---
category: private
layout: post
tags:
  - development
title: 'development - Visual Studio Memo'
---
## Visual Studio

https://docs.microsoft.com/ko-kr/cpp/porting/overview-of-potential-upgrade-issues-visual-cpp?view=vs-2019

### ToolSet
 /GL(전체 프로그램 최적화)을 사용하여 컴파일하는 경우 결과로 생성된 개체 파일은
이 파일을 생성하는 데 사용된 것과 동일한 도구 집합을 통해서만 연결할 수 있다.
 따라서 /GL 및 Visual Studio 2017(v141) 컴파일러를 사용하여 개체 파일을 생성하는 경우    Visual Studio 2017(v141) 링커를 사용하여 연결해야한다.
 개체 파일 내의 내부 데이터 구조가 도구 집합의 주 버전 간에 안정적이지 않으며
최신 도구 집합이 오래된 데이터 형식을 이해하지 못하기 때문.

<br>

### Library
 특정 버전의 Visua Studiol C++ 라이브러리 헤더 파일을 사용하여 소스 파일을 컴파일하는 경우(header #including 사용) 결과로 생성된 개체 파일은 동일한 버전의 라이브러리에만 연결해야 한다.
 
 예를 들어 소스 파일이 Visual Studio 2015 업데이트 3 <immintrin.h>를 사용하여 컴파일된 경우 Visual Studio 2015 업데이트 3 vcruntime 라이브러리에 연결해야 한다.
 
마찬가지로, 소스 파일이 Visual Studio 2017 버전 15.5 <iostream>을 사용하여 컴파일된 경우 Visual Studio 2017 버전 15.5 표준 C++ 라이브러리인 msvcprt에 연결해야 한다.
혼합 및 일치는 지원되지 않는다.

 C++ 표준 라이브러리의 경우 Visual Studio 2010 이후 표준 헤더에서 #pragma detect_mismatch를 사용하여 혼합 및 일치가 명시적으로 금지되었다.
 
 호환되지 않는 개체 파일이나 잘못된 표준 라이브러리에 연결을 시도하면 연결이 실패한다.
CRT(C runtime library)의 경우 혼합 및 일치가 지원된 적이 없지만, API 화면이 많이 변경되지 않았기 때문에 Visual Studio 2015 및 유니버설 CRT까지 작동하는 경우가 많았다.

 앞으로 이전 버전과의 호환성을 유지할 수 있도록 유니버설 CRT에서는 이전 버전과의 호환성을 중단했다.
 
 즉, 앞으로는 버전이 있는 새 유니버설 CRT binary을 도입할 계획이 없다.
대신, 기존 유니버설 CRT가 현재 위치에서 업데이트된다.

 이전 버전의 Microsoft C 런타임 헤더를 사용하여 컴파일된 개체 파일(및 라이브러리)과 부분 링크 호환성을 제공하기 위해 Visual Studio 2015 이상에서는 legacy_stdio_definitions.lib 라이브러리가 제공됩니다.

 이 라이브러리는 유니버설 CRT에서 제거된 대부분의 함수 및 데이터 내보내기에 대해 호환성 기호를 제공합니다.
 
 legacy_stdio_definitions.lib에서 제공하는 호환성 기호 집합은 Windows SDK에 포함된 라이브러리의 모든 종속성을 포함하여 대부분의 종속성을 충족하기에 충분하다.
 그러나 호환성 기능을 제공할 수 없는 유니버설 CRT에서 일부 기호가 제거되었다.
 이러한 기호로 일부 함수(예: __iob_func)와 데이터 내보내기(예: __imp___iob, __imp___pctype, __imp___mb_cur_max)가 있다.

<br>

### build setup
 소스 코드가 이미 잘 구성되었으며 이전 프로젝트가 Visual Studio 2010 이상으로 컴파일된 경우 이전 컴파일러와 새 컴파일러 둘 다에서 컴파일을 지원하도록 새 프로젝트 파일을 수동으로 편집할 수 있다.

 다음 예제에서는 Visual Studio 2015와 Visual Studio 2017 둘 다에 대해 컴파일하는 방법을 보여 준다.

 ```
 <PlatformToolset Condition="'$(VisualStudioVersion)'=='14.0'">v140</PlatformToolset>
<PlatformToolset Condition="'$(VisualStudioVersion)'=='15.0'">v141</PlatformToolset>
 ```