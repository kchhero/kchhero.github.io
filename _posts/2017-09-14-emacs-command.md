---
categories: Uncategoried
category: Tools
layout: post
tags:
  - emacs
title: 'emacs Command 정리'
---
#### 자주 사용하는(까먹는) Emacs Command 정리 요약

### Bookmark

https://www.emacswiki.org/emacs/BookMarks <br>

Emacs bookmarking makes use of three things that are related but different: a bookmark list, a bookmark file, and a bookmark-list display. Understanding these is important to using Emacs bookmarks. They are explained at Bookmark Basics.
Some bookmarking commands to get you started:
```
‘C-x r m’ – set a bookmark at the current location (e.g. in a file)
‘C-x r b’ – jump to a bookmark
‘C-x r l’ – list your bookmarks
‘M-x bookmark-delete’ – delete a bookmark by name
```
Your personal bookmark file is defined by option ‘bookmark-default-file’, which defaults to `~/.emacs.d/bookmarks` in the most recent Emacs versions and to `~/.emacs.bmk’ in older versions. The file is maintained automatically by Emacs as you create, change, and delete bookmarks.

<br>

(Bookmark+ makes it easy to have multiple bookmark files – different sets of bookmarks for different uses.)

The bookmark list (buffer ‘*Bookmark List*’) is like Dired or BufferMenu for bookmarks. You access it using ‘C-x r l’. (Emacs sometimes calls it the “bookmark menu list”, which is a misnomer.)

Some keys in ‘*Bookmark List*’:
```
‘a’ – show annotation for the current bookmark
‘A’ – show all annotations for your bookmarks
‘d’ – mark various entries for deletion (‘x’ – to delete them)
‘e’ – edit the annotation for the current bookmark
‘m’ – mark various entries for display and other operations, (‘v’ – to visit)
‘o’ – visit the current bookmark in another window, keeping the bookmark list open
‘C-o’ – switch to the current bookmark in another window
‘r’ – rename the current bookmark
```

<br>

### Quick calculate
```
quick 진법변환 : C-x * q 

	 Quick calc mode : 10#255   ==> 10진수 255에 대한 16,8,2진수 표시
	 Quick calc mode : 16#ABCD   ==>  16진수 0xABCD에 대한 16,8,2진수 표시
	 Quick calc mode : 16#90000-10#1024    ==> 16진수 0x90000 - 10진수 1K 계산
	 Result: 589824 - 1024 =>  588800  (16#8FC00, 8#2176000, 2#10001111110000000000)
```

<br>

### tab indent
	M-x tabify: Change spaces to tabs where appropriate.
	M-x untabify: Change all tabs to the correct number of spaces (controlled by tab-width).

<br>

### All select
	M-x : dired-toggle-marks
