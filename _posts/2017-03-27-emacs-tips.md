---
category: Tools
layout: post
tags:
  - emacs
title: 'emacs tips'
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

---

#### email 보내기 
.emacs setting

```shell
;;nexell setting
;; C-x m  or  M-x message-mail
;; M-x mml-attach-file
;; M-x message-send
(setq mail-host-address "gmail.com")
(setq user-mail-address "suker@nexell.co.kr")
(setq send-mail-function (quote smtpmail-send-it))
(setq smtpmail-smtp-server "smtp.gmail.com")
(setq smtpmail-smtp-service 587)
(setq smtpmail-auth-credentials (quote (("smtp.gmail.com" 587 "suker@nexell.co.kr" nil))))
(setq smtpmail-starttls-credentials (quote (("smtp.gmail.com" 587 nil nil))))
(setq user-full-name "choonghyun Jeon")
(custom-set-variables
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(send-mail-function (quote smtpmail-send-it))
 '(smtpmail-smtp-server "smtp.gmail.com")
 '(smtpmail-smtp-service 25))
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 )
```