---
layout: post
tags:
  - Language
title: 'printk format 정리'
category: language
---
| f variable is of Type | use printk format specifier |
| -------------------------- | ---------------------------------- |
| int                           | %d or %x |
| unsigned int           |  %u or %x |
| long                        | %ld or %lx |
| unsigned long        |   %lu or %lx |
| long long                | %lld or %llx |
| unsigned long long |   %llu or %llx |
|                size_t            |           %zu or %zx |
|                ssize_t          |            %zd or %zx |


더 자세하고 많은 내용은 아래 link 참고.

[https://www.kernel.org/doc/Documentation/printk-formats.txt](https://www.kernel.org/doc/Documentation/printk-formats.txt)
