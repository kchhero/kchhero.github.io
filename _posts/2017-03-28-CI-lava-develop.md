---
title: 'LAVA develop'
layout: post
tags:
  - Continuous Integration
category: CI
---
#### Outline

Docker image base로 lava-dispatcher와 lava-server 를 구축한 뒤에 nexell target에 맞도록 extension 하도록 하였다.
target board는 NXP4330 navi board다.

아래의 Dockerfile은 docker hub의 forcedinductionz/lava-docker image를 base로 extension 한것이다.
https://hub.docker.com/r/forcedinductionz/lava-docker/

lava-test-runner를 fusing하여 구동시키기 위해서 android sdk를 설치하도록 하였다.
lava-dispatcher의 deploy method에는 lxc, fastboot 등등이 있는데, 그대로 사용하기 어렵다.

https://hub.docker.com/r/suker/lava-docker/

```shell
FROM forcedinductionz/lava-docker

RUN apt-get update && apt-get install -y emacs24

# Install all dependencies
RUN apt-get update && \
    apt-get install -y wget openjdk-7-jre-headless libc6-i386 lib32stdc++6 && \
    apt-get clean && \
    apt-get autoclean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install android tools + sdk
ENV ANDROID_HOME /opt/android-sdk-linux
ENV PATH $PATH:${ANDROID_HOME}/tools:$ANDROID_HOME/platform-tools

# Set up insecure default key
RUN mkdir -m 0750 /.android
ADD adb/insecure_shared_adbkey /.android/adbkey
ADD adb/insecure_shared_adbkey.pub /.android/adbkey.pub

RUN wget -qO- "http://dl.google.com/android/android-sdk_r24.3.4-linux.tgz" | tar -zx -C /opt && \
            echo y | android update sdk --no-ui --all --filter platform-tools --force

COPY ./etc /etc
COPY ./home /home
COPY ./usr /usr

COPY .bashrc /root/

RUN mkdir -p /var/lib/nexell
```

---

#### Run Docker image
```shell
docker run -it --name con_lava -v /boot:/boot -v /lib/modules:/lib/modules -v /home/suker/LAVA/docker/fileshare:/opt/fileshare \
-v /dev/bus/usb:/dev/bus/usb -v ~/.ssh/id_rsa_lava.pub:/home/lava/.ssh/authorized_keys:ro --device=/dev/ttyUSB0 -p 8000:80 -p 2022:22 \
-h lava-slave --privileged -v /sys/fs/cgroup:/sys/fs/cgroup suker/lava-docker
```
---

#### /etc
/etc/ser2net.conf 에 아래 코드를 추가한다. 물리 device의 usb 연결 node 정보에 대한 config 이다.
```shell
4001:telnet:600:/dev/ttyUSB0:115200 8DATABITS NONE 1STOPBIT banner
```

#### /home
device-dictionary에 추가할 device 정보 및 device-type이다.
/home/suker/LAVA/docker/home/lava/lava-server/lava_scheduler_app/tests/devices/s5p4418-01.jinja2
/home/suker/LAVA/docker/home/lava/lava-server/lava_scheduler_app/tests/device-types/s5p4418.jinja2


#### /usr
lava-dispatcher extension source는 아래와 같다.
 [==> git patch](https://github.com/kchhero/kchhero.github.io/blob/master/_posts_data/0001-suker-lava-dispatcher-nexell-extension.patch "patch")

```shell
[suker@suker-nexell] ~/LAVA/lava-dispatcher
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   lava_dispatcher/pipeline/actions/boot/fastboot.py
	modified:   lava_dispatcher/pipeline/actions/boot/lxc.py
	modified:   lava_dispatcher/pipeline/actions/deploy/apply_overlay.py
	modified:   lava_dispatcher/pipeline/actions/deploy/fastboot.py
	modified:   lava_dispatcher/pipeline/actions/deploy/lxc.py
	modified:   lava_dispatcher/pipeline/connections/lxc.py
	modified:   lava_dispatcher/pipeline/protocols/lxc.py
	modified:   lava_dispatcher/pipeline/utils/constants.py

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	lava_dispatcher/pipeline/connections/telnet.py

no changes added to commit (use "git add" and/or "git commit -a")
[suker@suker-nexell] ~/LAVA/lava-dispatcher
$
```

---

#### 작업 완료후 container에서 backup할 파일은 다음과 같다.
```shell
#!/bin/bash

declare -a backuplist=(
        "/etc/ser2net.conf" \
        "/home/lava/bin/nexell-lava-commands.sh" \
        "/home/lava/bin/submit-nexell-testjob.sh" \
        "/home/lava/bin/s5p4418-navi-ref-tiny_job_normal.yaml" \
        "/home/lava/bin/s5p4418-navi-ref-tiny_job_normal_test.yaml" \
        "/home/lava/lava-server/lava_scheduler_app/tests/devices/s5p4418-01.jinja2" \
        "/home/lava/lava-server/lava_scheduler_app/tests/device-types/s5p4418.jinja2" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/actions/deploy/apply_overlay.py" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/actions/deploy/fastboot.py" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/actions/deploy/lxc.py" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/actions/boot/fastboot.py" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/actions/boot/lxc.py" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/connections/lxc.py" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/connections/telnet.py" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/protocols/lxc.py" \
        "/usr/lib/python2.7/dist-packages/lava_dispatcher/pipeline/utils/constants.py" \
        )

for i in ${backuplist[@]}
do
    docker cp con_lava:$i .$i
done
```

---

실제로 lava-dispatcher를 실행시키기 위한 .yaml 및 shell script는 Bitbucket 에 올라가있는것을 참고.
**git clone https://kchhero@bitbucket.org/kchhero/others.git**
