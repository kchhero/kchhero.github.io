---
tags: docker
title: 'Docker 정리'
layout: post
published: true
category: docker
description: docker 관련 명령어 및 정리 내용
---
#### pull 
-  sudo docker pull suker/mydesktoptest

#### container 확인
- $ docker ps -a

#### images 확인
- $ docker images

#### image를 hello container 이름으로 실행하기
- $ docker run -i -t --name hello ubuntu /bin/bash
- $ docker run -i -t --name suker0.4 suker:0.4 /bin/bash

- $ docker restart hello
- $ docker attach hello      -->   정지: exit Ctrl+D, 그냥나오기: Ctrl+P, Ctrl+Q


#### container 삭제
- $ docker rm hello

#### image 삭제
- $ docker rmi ubuntu:latest

#### image 생성
- $ docker build --tag suker:0.3 ./

#### image 실행
- $ docker run --name hello-nginx -d -p 80:80 -v /root/data:/data hello:0.1

#### history 확인
- $ docker history suker:0.3

#### container -> host   file copy
- $ docker cp suker-test:/etc/xxx.conf ./
- $ docker cp <container name>:<path> <host path>

#### container 의 변경 사항을 image로 생성
- file 변경 후 ...
  docker commit -a "suker@nexell.co.kr" -m "add test.sh" suker-test suker:0.3

#### diff
- docker diff suker-test

#### git show
- docker inspect suker-test

#### tag및 push
- $ docker tag image_id myname/server:latest
- $ docker push suker/mydesktoptest

#### docker별 이미지 생성시 프로그램별로 이미지를 생성 --> container간 연결이 필요
- $ docker run --name db -d mongo   -->  db라는 이름으로 mongo DB를 실행

#### --link -->  container간의 연결
- $ docker run --name web -d -p 80:80 --link db:db nginx

#### socat을 이용하여 다른 서버의 container에 연결

---

#### 모든 컨테이너 삭제하기
> docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

#### 모든 이미지 삭제하기
> docker rmi $(docker images -q)

#### untagged 이미지 삭제하기
> docker rmi $(docker images | grep "^<none>" | awk "{print $3}")

#### Exit 상태의 모든 컨테이너 삭제하기
> docker rm $(docker ps --filter 'status=exited' -a -q)

---

### Refernce
>https://github.com/jenkinsci/docker.git 
https://github.com/ubuntu-core/jenkins-ubuntu/blob/master/Dockerfile

---

### Trouble shooting

>jenkins login 안될때
JENKINS_HOME folder and edit here the file config.xml:
<useSecurity>true</useSecurity>  ==>  <useSecurity>false</useSecurity>
remove <authorizationStrategy …> until </authorizationStrategy>
<securityRealm..> until </securityRealm>

---

>docker build 문제
"""
W: Failed to fetch http://deb.debian.org/debian/dists/jessie-backports/main/binary-amd64/Packages  Hash Sum mismatch
E: Some index files failed to download. They have been ignored, or old ones used instead.
"""
original jenkins에서 openjdk:8-jdk 를 openjdk:9-jdk 로 수정해서 생성함


