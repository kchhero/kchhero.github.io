---
title: 'emacs etags command'
layout: post
tags:
  - emacs
category: Tools
---
#### emacs etags 설정 및 command

### TAGS 생성
```
find . -name "*.[chCH]" -print | xargs etags -a -o TAGS
```

<br>

### TAGS 파일 경로 지정
```
M-x visit-tags-table
```

<br>

### ETAGS-KEY-BINDINGS
```
| | |
| ------------ | ------------ |
| M-! | etags *.[ch] index .c and .h files in current directory |
| C-u M-x | `visit-tags-table'	set index file for current buffer |
| M-x | `visit-tags-table'	globally set index file |
| M-. | go to definition of symbol in index |
| C-M-. | go to definition for a regular expression in index |
| C-u M-. | go to next definition |
| M-- M-. | go to previous definition |
| M-* | return back to before you started |
| M-x | `tags-search'	go to entry for regular expression in index |
| M-, | go to next entry in index |
| M-x | `tags-query-replace'	search and replace for regular expression |
| M-TAB | complete tag at point |
| C-u M-TAB | complete language symbol, avoid tags, at point |
```