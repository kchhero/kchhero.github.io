---
layout: post
tags: tools
title: Suker Tray
category: Tools
description: 2013년쯤에 AndroSuker를 만들다가 중간에 하나 만든 Tray tool.
---

- 사내 server login 할때마다 id/pw 넣는게 귀찮아서 tray에 써놓고 one click으로 loging 하려고 만들었다가 아래 그림처럼 PC의 바탕화면에 빼놓기 애매한 각종 shortcut 들을 한군데에 담아두고 double-click 만으로 실행하는 기능 추가함.

```shell?line_number=false
Language : JAVA, Perl
개발환경 : window XP, window7 32bit, Eclipse
layout : UI tool kit으로 SWT를 사용.
배포tool : Launch4j
```
소스 및 실행파일은 회사 보안 정책상 upload가 안되어 못올림.
공개하기 민감한 부분은 지우고 올림.

우측의 Do 버튼 click 또는 list item을 double-click하면 실행됨.
add/del 버튼으로 item을 추가/삭제 할 수 있음.
Up/Down 버튼으로 item의 위치를 바꿀 수 있음.
우측 상단의 Alpha 값은 해당 tool의 transparent 를 결정함.
Andvance 버튼을 누르면 shortcut tray list가 사라짐.

![](/assets/ext_images/tray.PNG)
