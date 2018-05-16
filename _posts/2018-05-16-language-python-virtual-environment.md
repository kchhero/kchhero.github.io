---
title: 'Python> Python virtutal environment'
layout: post
tags:
  - Language
category: programming
---
#### pyenv (https://github.com/pyenv) 설치 / 환경변수 설정
```
$ curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
$ echo 'export PATH="/home/suker/.pyenv/bin:$PATH"' > ~/.bash_profile
$ echo 'eval "$(pyenv init -)"' >> ~/.bash_profile 
$ echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bash_profile 
$ source ~/.bash_profile
```

<br>

#### pyenv 를 이용한 python 설치
```
$ pyenv install 2.7.15
$ pyenv install 3.6.5
```
확인 ==>
```
$ ls -al ~/.pyenv/versions/
$ pyenv versions
* system (set by /home/suker/.pyenv/version)
  2.7.15
  3.6.5
```

<br>

#### 현재 python 버전 변경
```
$ pyenv global 3.6.5
$ pyenv global 2.7.15
```

<br>

#### pyenv + virtualenv
생성
```
$ pyenv virtualenv 2.7.15 py2envvv
$ pyenv virtualenv 3.6.5 py3envvv
$ ll ~/.pyenv/versions/
total 16K
drwxrwxr-x  4 suker suker 4K  5월 16 17:20 ./
drwxrwxr-x 14 suker suker 4K  5월 16 17:15 ../
drwxr-xr-x  7 suker suker 4K  5월 16 17:20 2.7.15/
drwxr-xr-x  7 suker suker 4K  5월 16 17:15 3.6.5/
lrwxrwxrwx  1 suker suker 1K  5월 16 17:20 py2envvv -> /home/suker/.pyenv/versions/2.7.15/envs/py2envvv/
lrwxrwxrwx  1 suker suker 1K  5월 16 17:15 py3envvv -> /home/suker/.pyenv/versions/3.6.5/envs/py3envvv/
```

실행 / 종료
```
$ pyenv activate py3envvv
$ pyenv deactivate
```


<br>
----
<br>

#### virtualenv 설치
```
$ sudo pip install launchpadlib
$ sudo pip install virtualenv virtualenvwrapper

$ sudo pip3 install launchpadlib
$ sudo pip3 install virtualenv virtualenvwrapper
```

<br>

#### virtualenv 생성/실행
```
$ virtualenv --python=python2.7 py2env
$ virtualenv --python=python3.6 py3env
```

<br>

#### virtualenv 진입
```
$ source py2env/bin/activate
또는
$ source py3env/bin/activate
(py3env) [suker@suker-machine] ~/pythonEnv
```

<br>

#### virtualenv 종료
```
$ deactivate
```

