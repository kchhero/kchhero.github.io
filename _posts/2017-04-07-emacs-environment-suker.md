---
categories: Uncategoried
category: Tools
layout: post
tags:
  - emacs
title: 'emacs environment for SUKER'
---
#### emacs 환경 구성에 대한 정리

emacs 를 사용한지 3년이 조금 넘는 시간동안 .emacs와 .emacs.d 는 걸레(?)가 되었다.
모든것을 정리하고 설치부터 새롭게 정리하려고 한다.
<br>
##### ( 참고: http://ergoemacs.org/emacs/emacs_package_system.html )

---

##### 0. sudo apt-get update && sudo apt-get install -y emacs24
<br>
##### 1. melpa for package install
```lisp
--- .emacs ---
(require 'package) ;; You might already have this line
(add-to-list 'package-archives
        '("melpa" . "https://stable.melpa.org/packages/"))
(when (< emacs-major-version 24)  ;; For important compatibility libraries like cl-lib
        (add-to-list 'package-archives '("gnu" . "http://elpa.gnu.org/packages/")))
(package-initialize) ;; You might already have this line
```
<br>
##### 2. package 설치
	M-x package-list-packages
	
	【Enter ↵】 (package-menu-describe-package) → Describe the package under cursor.
	【i】 (package-menu-mark-install) → mark for installation.
	【u】 (package-menu-mark-unmark) → unmark.
	【d】 (package-menu-mark-delete) → mark for deletion (removal of a installed package).
	【x】 (package-menu-execute) → for “execute” (start install/uninstall of marked items).
	【r】 (package-menu-refresh) → refresh the list from server.

* 설치 list : cider, clojure-cheatsheet, clojure-mode-ex... ,clojure-quick-r... ,clojure-snippets, color-theme-sanityinc-tomorrow, elpy, gmail-message-mode, python, python-environment, python-mode, Flycheck, web-mode, function-args
* 설치 list2 : multi-term (terminal), swiper (search & buffer간 이동),
* 설치 list3 : epc, jedi, el-get, auto-complete, dimmer
	* jedi dependancy
		```
		pip install jedi
		pip install epc
		apt-get install virtualenv
		```
		M-x  jedi:install-server
		```
		==> .emacs setting
		;; python assist
		(load-file "~/.emacs.d/elpa/jedi-0.2.7/jedi.el")
		(add-hook 'python-mode-hook 'jedi:setup)
		(setq jedi:complete-on-dot t)                 ; optional
		```


<br>

##### 3. theme 설정
( 참고 : https://github.com/purcell/color-theme-sanityinc-tomorrow )

	M-x package-install RET color-theme-sanityinc-tomorrow RET
	위에서 이미 설치했으므로 pass 
	M-x color-theme-sanityinc-tomorrow-blue

3번까지 진행 후 .emacs 를 보면 아래와 같이 변경 되어있을거다.
```lisp
(require 'package) ;; You might already have this line
(add-to-list 'package-archives
              '("melpa" . "https://stable.melpa.org/packages/"))
(when (< emacs-major-version 24)
   ;; For important compatibility libraries like cl-lib
      (add-to-list 'package-archives '("gnu" . "http://elpa.gnu.org/packages/")))
(package-initialize) ;; You might already have this line
(custom-set-variables
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(ansi-color-faces-vector
   [default bold shadow italic underline bold bold-italic bold])
 '(ansi-color-names-vector
   (vector "#c5c8c6" "#cc6666" "#b5bd68" "#f0c674" "#81a2be" "#b294bb" "#8abeb7" "#373b41"))
 '(custom-enabled-themes (quote (sanityinc-tomorrow-blue)))
 '(custom-safe-themes
   (quote
    ("82d2cac368ccdec2fcc7573f24c3f79654b78bf133096f9b40c20d97ec1d8016" "06f0b439b62164c6f8f84fdda32b62fb50b6d00e8b01c2208e55543a6337433a" default)))
 '(fci-rule-color "#373b41")
 '(vc-annotate-background nil)
 '(vc-annotate-color-map
   (quote
    ((20 . "#cc6666")
     (40 . "#de935f")
     (60 . "#f0c674")
     (80 . "#b5bd68")
     (100 . "#8abeb7")
     (120 . "#81a2be")
     (140 . "#b294bb")
     (160 . "#cc6666")
     (180 . "#de935f")
     (200 . "#f0c674")
     (220 . "#b5bd68")
     (240 . "#8abeb7")
     (260 . "#81a2be")
     (280 . "#b294bb")
     (300 . "#cc6666")
     (320 . "#de935f")
     (340 . "#f0c674")
     (360 . "#b5bd68"))))
 '(vc-annotate-very-old-color nil))
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 )
```

<br>

##### 4. customizing, .emacs &&  .emacs.d
.emacs에 아래 처럼 code를 추가하고, .emacs.d 에도 필요한 .el 파일들을 copy 해준다.
 https://github.com/kchhero/suker_enviroment/emacs_old/.emacs.d/
```lisp
;------------------------- suker customize start ---------------------------
;; buffer move, window move
;;(add-to-list 'load-path "~/.emacs.d/")
(load-file "~/.emacs.d/buffer-move.el")
(require 'buffer-move)
(global-set-key (kbd "C-c <up>")     'buf-move-up)
(global-set-key (kbd "C-c <down>")   'buf-move-down)
(global-set-key (kbd "C-c <left>")   'buf-move-left)
(global-set-key (kbd "C-c <right>")  'buf-move-right)

;; for yocto recipe edit mode 
(load-file "~/.emacs.d/bb-mode.el")
(setq auto-mode-alist (cons '("\\.bb$" . bb-mode) auto-mode-alist))
(setq auto-mode-alist (cons '("\\.inc$" . bb-mode) auto-mode-alist))
(setq auto-mode-alist (cons '("\\.bbappend$" . bb-mode) auto-mode-alist))
(setq auto-mode-alist (cons '("\\.bbclass$" . bb-mode) auto-mode-alist))
(setq auto-mode-alist (cons '("\\.conf$" . bb-mode) auto-mode-alist))
(setq auto-mode-alist (cons '("\\Dockerfile$" . bb-mode) auto-mode-alist))

;; navi menu on/off
;; https://github.com/ancane/emacs-nav/blob/master/nav.el
(add-to-list 'load-path "~/.emacs.d/emacs-nav-49/")
(require 'nav)
(nav-disable-overeager-window-splitting)
(global-set-key (kbd "<f8>") 'nav-toggle)

;; python setting
(load-file "~/.emacs.d/python-init.el")
(require 'elpy)
(elpy-enable)

(require 'flycheck)
(add-hook 'after-init-hook #'global-flycheck-mode)

(require 'function-args)
(fa-config-default)

(require 'web-mode)
(add-to-list 'auto-mode-alist '("\\.tpl\\'" . web-mode))
;;------------------------ suker customize End ---------------------------
```

<br>

##### 5. customize
status bar 에 file name 을 비롯하여 path 까지 표시해줄 때 .emacs 에 아래와 같이 code를 추가해주면 된다.
```
(setq-default mode-line-buffer-identification
              (let ((orig  (car mode-line-buffer-identification)))
                `(:eval (cons (concat ,orig (abbreviate-file-name default-directory))
                              (cdr mode-line-buffer-identification)))))
```

<br>

##### 6. Font 설정, Hack font
http://sourcefoundry.org/hack/
여기에서 .zip file download --> Hack-Regular.ttf  double click --> install
```
(when (eq system-type 'darwin)
      (set-default-font "-*-Hack-normal-normal-normal-*-12-*-*-*-m-0-iso10646-1"))
```