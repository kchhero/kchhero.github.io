---
title: 'emacs tips'
layout: post
tags:
  - emacs
category: Tools
---
#### elpy

reference : https://github.com/jorgenschaefer/elpy

```lisp
One-line install: pip install jedi flake8 importmagic autopep8

Evaluate this in your *scratch* buffer:

(require 'package)
(add-to-list 'package-archives
             '("elpy" . "https://jorgenschaefer.github.io/packages/"))
Then run M-x package-refresh-contents to load the contents of the new repository, and M-x package-install RET elpy RET to install elpy.

Finally, add the following to your .emacs:

(package-initialize)
(elpy-enable)
Done.
```