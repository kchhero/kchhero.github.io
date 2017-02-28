---
title: tensorflow-install
layout: post
tags:
  - ML
category: MachineLearning
---
##### Docker + tensorflow

1. Base image tensorflow/tensorflow 
==> docker pull tensorflow/tensorflow
==> extension suker/tensorflow

2. extension
```shell
==> Dockerfile
FROM tensorflow/tensorflow:latest
MAINTAINER suker <suker@nexell.co.kr>
COPY run_tensorboard.sh /          ==>  tensorboard --logdir=/notebooks/board
```

3.  실행
```shell
#!/bin/bash
docker run -d --name tenf -p 8888:8888 -p 6006:6006 -v ~/MachineLearning/shared:/notebooks -e PASSWORD=nexell suker/tensorflow
sleep 3
docker exec -d tenf /run_tensorboard.sh
```

---

8888 port는 jupyter notebook, 6006 port는 tensorboard 이다.
각각 locahost:8888 및 localhost:6006 에 접속하면 사용 가능하다.