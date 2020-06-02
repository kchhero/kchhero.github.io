---
title: 'development - VScode + RUST'
layout: post
tags:
  - development
category: private
---
## RUST 설치

https://github.com/rust-lang/rustup#other-installation-methods
==> other installation methods 에서 x86_64-pc-windows-msvc 선택
==> rustup-init.ext 설치

<br>

## VSCode 에서
GDB install

Ctrl+P
```
ext install rust
ext install native-debug
ext install CodeLLDB
```
new terminal
```
$ cargo new hello_world --bin
$ cargo new hello_world --bin
$ code ./hello_world
$ rustup default nightly
```
