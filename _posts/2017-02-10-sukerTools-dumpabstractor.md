---
layout: post
tags: tools
title: SukerRamDump_Abstractor
category: Tools
description: 2014년 python 공부를 시작하면서 만들어봄.
---

- 사내 server login 할때마다 id/pw 넣는게 귀찮아서 tray에 써놓고 one click으로 loging 하려고 만들었다가 아래 그림처럼 PC의 바탕화면에 빼놓기 애매한 각종 shortcut 들을 한군데에 담아두고 double-click 만으로 실행하는 기능 추가함.

```shell?line_number=false
Language : Python 2.7
개발환경 : window7 32bit, Spyder 2.2.5
UI 제작 tool & Library : wxGlade, wx
packageing : Inno Script Studio
```
소스 및 실행파일은 회사 보안 정책상 upload가 안되어 못올림.

목적 ; vmlinux가 없는 상태에서는 Dump file 만으로 debugging이 어려움.
winhex 나 ultra editor로 열어서 볼 수 있지만 불편함. 이 tool을 이용하면 dump 에 남아있는
kmesg를 뽑아 내기 쉬움.

![](/assets/ext_images/ramdumpabstractor.PNG)

==> KitKat 까지는 잘 동작하였는데, Lolipop 의 kernel version 이 3.10.x 로 올라오면서 kernel log를 저장하는 알고리즘이 바뀐듯함. 이전처럼 kernel log가 연속된 공간에 남아있지 않아 더이상 사용 불가... ㅜㅜ