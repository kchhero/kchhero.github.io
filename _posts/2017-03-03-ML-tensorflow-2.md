---
title: tensorflow-install-GPU
layout: post
tags:
  - ML
category: MachineLearning
---
##### Docker + tensorflow + GPU version

아래는 suker/tensorflow 를 GPU mode로 돌리기 위한 설치 방법 및 실행 방법이다.

nvidia driver install
```
sudo apt-get install nvidia-current
```

```
sudo apt-get install -y nvidia-modprobe
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker_1.0.1-1_amd64.deb
sudo dpkg -i /tmp/nvidia-docker*.deb && rm /tmp/nvidia-docker*.deb
```

nvidia-docker run -it -p 8888:8888 tensorflow/tensorflow:latest-gpu
