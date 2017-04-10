---
title: Python-shortcut-Script
layout: post
tags:
  - python
category: programming
---
#### my shortcut script
python 을 이용하여 귀찮은 typing 을 줄이고자 한다. ssh connect라던가 git 명령어 또는
scp 등등 server to local, local to server에 대한 command는 ip address 및 path 지정등등 일일이 typing 하기 귀찮다.
대략 아래와 같이 구성하여 사용하고 있다.

interactive한 mini shell 을 실행하는 것 또한 단축키로 지정하여 사용하였다. (이것 마저도 귀찮아서)
<br>

##### 단축키 F8 에 실행할 script 연결
[https://www.youtube.com/watch?v=0nxU3tDBpgg](https://www.youtube.com/watch?v=0nxU3tDBpgg)

```
==>  F8에 대한 binary 값을 얻어옴.   od -c <<< "Ctrl+V F8"  

$ od -c <<< ^[[18~
0000000 033   [   1   8   ~  \n
0000006

==>  실제 binding
$ bind '"\e[19": "~/asdf.sh"'
```
<br>
~/asdf.sh 에서 하는일은 단순하다. xxx.py 를 실행시켜주기만 한다.
[https://github.com/kchhero/suker_python_project/blob/master/sukerScripts/nexell.py](https://github.com/kchhero/suker_python_project/blob/master/sukerScripts/nexell.py)
```shell

===============================================================================
Choice script : LEVEL 1
===============================================================================
select : 
    'ss' or 'ssh' --> search scripts can show or run
    'gi' or 'git' --> search scripts can show or run
    're' or 'repo' --> search scripts can show or run
    'su' or 'sudo scp -r' --> search scripts can show or run
    'mo' or 'mount' --> search scripts can show or run

    'q' or 'exit' or 'quit'  --> Quit this program
===============================================================================

 >>> ss
 ** ==>  working Path /home/suker/repoWithYocto/tools
===============================================================================
Choicet want script LEVEL 2
===============================================================================
select : 
    0 : ssh suker@192.168.1.16 --> select number...
    1 : ssh jenkins@192.168.1.26 --> select number...
    2 : ssh nexellstorage@192.168.1.25 --> select number...
    3 : ssh bok@192.168.1.15 --> select number...
    'b' --> back menu
    'q' or 'exit' or 'quit' --> Quit this program
===============================================================================

===============================================================================
Choicet want script LEVEL 2
===============================================================================
select : 
    0 : scp -r <src> <dest> --> select number...
    1 :  --> select number...
    'b' --> back menu
    'q' or 'exit' or 'quit' --> Quit this program
===============================================================================

 >>> 0
 ** ==>  working Path /home/suker/repoWithYocto/tools
===============================================================================
Choicet want script LEVEL 3
===============================================================================
select argument : 
    0 : nexellstorage@192.168.1.25:/home/nexellstorage/snapshot/yocto/ --> select number...
    1 : nexellstorage@192.168.1.25:/home/nexellstorage/releases/NEXELL-YOCTO-SDKs --> select number...
    2 : jenkins@192.168.1.26:/home/jenkins/Jenkins_Storage/jobs --> select number...
    3 : jenkins@192.168.1.26:/home/jenkins/Jenkins_Storage/backup_conf --> select number...
    'b' --> back menu
    'q' or 'exit' or 'quit' --> Quit this program
===============================================================================

 >>> arg1:

```