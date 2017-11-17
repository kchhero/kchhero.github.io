---
category: Uncategoried
layout: post
tags:
  - linux
title: sponge
---
#### sponge 사용법 요약

### Install

install : sudo apt install sponge

### Man page

NAME
 	sponge - soak up standard input and write to a file

<br>

SYNOPSIS
	sed '...' file | grep '...' | sponge [-a] file

<br>

DESCRIPTION
```
sponge reads standard input and writes it out to the specified file. Unlike a shell redirect, sponge soaks up all its input before writing the output file. 
This allows constructing pipelines that read from and write to the same file.
sponge preserves the permissions of the output file if it already exists.
When possible, sponge creates or updates the output file atomically by renaming a temp file into place. (This cannot be done if TMPDIR is not in the same filesystem.)
If the output file is a special file or symlink, the data will be written to it, non-atomically.
If no file is specified, sponge outputs to stdout.
```

<br>

OPTIONS
```
 -a
Replace the file with a new file that contains the file's original content, with the standard input appended to it. This is done atomically when possible.
```

<br>

AUTHOR<br>
Colin Watson and Tollef Fog Heen
