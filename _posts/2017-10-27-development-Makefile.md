---
title: 'development - Makefile'
layout: post
tags:
  - development
category: private
---
description : makefile 중요사항 / tips
### Makefile

#### STRIP 과 gdb
gdb 실행시 아래와 같이 symbol 정보를 load하지 못하는 경우가 있다.
처음에는 '-g' 옵션 또는 최적화 문제 '-Ox' 옵션 문제로 생각했는데, STRIP 을 제거 했더니 정상적으로 symbol enable된 ELF 가 생성되었다.
strip의 기능을 제대로 모르고 있었기때문이다.
```
$ arm-cortex_a9-linux-gnueabi-strip --help
Usage: arm-cortex_a9-linux-gnueabi-strip <option(s)> in-file(s)
 Removes symbols and sections from files
 The options are:
  -I --input-target=<bfdname>      Assume input file is in format <bfdname>
  -O --output-target=<bfdname>     Create an output file in format <bfdname>
  -F --target=<bfdname>            Set both input and output format to <bfdname>
  -p --preserve-dates              Copy modified/access timestamps to the output
  -D --enable-deterministic-archives
                                   Produce deterministic output when stripping archives
  -U --disable-deterministic-archives
                                   Disable -D behavior (default)
  -R --remove-section=<name>       Remove section <name> from the output
  -s --strip-all                   Remove all symbol and relocation information
  -g -S -d --strip-debug           Remove all debugging symbols & sections
     --strip-dwo                   Remove all DWO sections
     --strip-unneeded              Remove all symbols not needed by relocations
     --only-keep-debug             Strip everything but the debug information
  -N --strip-symbol=<name>         Do not copy symbol <name>
  -K --keep-symbol=<name>          Do not strip symbol <name>
     --keep-file-symbols           Do not strip file symbol(s)
  -w --wildcard                    Permit wildcard in symbol comparison
  -x --discard-all                 Remove all non-global symbols
  -X --discard-locals              Remove any compiler-generated symbols
  -v --verbose                     List all object files modified
  -V --version                     Display this program's version number
  -h --help                        Display this output
     --info                        List object formats & architectures supported
  -o <file>                        Place stripped output into <file>
arm-cortex_a9-linux-gnueabi-strip: supported targets: elf32-littlearm elf32-bigarm elf32-little elf32-big plugin srec symbolsrec verilog tekhex binary ihex

```
