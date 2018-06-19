---
title: 'Dockerfile 정리'
layout: post
tags:
  - docker
category: docker
---
### dockerfile 사용관련

#### container 에서 user passwd 사용하기
```
RUN useradd -m suker && echo "suker:suker" | chpasswd && adduser suker sudo
```

#### locale 설정
```
RUN apt-get install -y locales
RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    locale-gen
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
```
