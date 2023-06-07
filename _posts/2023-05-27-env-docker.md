---
title: 如何使用docker部署环境
tags: "开发环境配置"
key: env-docker
---

本文介绍了如何使用docker快速部署开发环境。<!--more-->

Docker的安装方法各个操作系统不尽相同，网上也有很多的相关资料，这里给出[官网链接](https://www.docker.com/products/docker-desktop/)，其他不再赘述，主要介绍了如何使用docker部署开发环境。

在正式使用docker之前，请先确保已经安装好，在命令行中输入

```bash
docker --version
```
指令正常运行即代表docker已经安装成功。

# 什么是docker

docker顾名思义，就是一个容器，类似于虚拟机，但是比虚拟机更加轻量级，更加便捷。docker的镜像是一种轻量级的虚拟机镜像，可以快速的启动，而且占用的资源也比虚拟机少很多。使用docker时，要区分好镜像和容器的概念，镜像是一个静态的文件，容器是一个动态的实例。类似于使用虚拟机时，需要使用虚拟机镜像来创建虚拟机实例，虚拟机的实例就是容器，对容器做的修改不会影响到镜像（可以手动将容器保存为镜像）。

docker可以快速的进行应用分发与部署，通过虚拟化技术统一程序运行环境，**确保不同硬件、操作系统的机器上运行的是完全相同的环境**。

# 获取镜像

docker的镜像可以从docker hub上获取，也可以从其他地方获取，比如从本地的镜像文件导入。docker hub上有很多的镜像，可以直接使用，也可以根据自己的需要进行修改。从docker hub拉取镜像的指令为

```bash
docker pull [镜像名]:[版本号]
```

也可以使用dockerfile来创建镜像，dockerfile是一个文本文件，里面包含了创建镜像的命令，可以通过dockerfile来创建镜像，也可以通过dockerfile来修改镜像。

下面是一个dockerfile的实例：

```dockerfile
FROM --platform=linux/amd64 ubuntu:20.04

ENV TZ=Asia/Shanghai
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONPATH=${PYTHONPATH}:/python-sdk/lib/python2.7/site-packages
ENV QI_SDK_PREFIX=/python-sdk

RUN apt update
RUN apt install -y wget python libglib2.0-dev libsm6 libxrender1 libxext6 build-essential cmake
RUN wget https://bootstrap.pypa.io/pip/2.7/get-pip.py
RUN wget https://community-static.aldebaran.com/resources/2.8.6/pynaoqi-python2.7-2.8.6.23-linux64-20191127_152327.tar.gz
RUN wget https://community-static.aldebaran.com/resources/2.8.5/naoqi-sdk-2.8.5.10-linux64.tar.gz
RUN tar -xzvf pynaoqi-python2.7-2.8.6.23-linux64-20191127_152327.tar.gz && rm pynaoqi-python2.7-2.8.6.23-linux64-20191127_152327.tar.gz
RUN tar -xzvf naoqi-sdk-2.8.5.10-linux64.tar.gz && rm naoqi-sdk-2.8.5.10-linux64.tar.gz
RUN mv pynaoqi-python2.7-2.8.6.23-linux64-20191127_152327 python-sdk
RUN mv naoqi-sdk-2.8.5.10-linux64 naoqi-sdk
RUN python get-pip.py
RUN pip install opencv-python==4.2.0.32 Pillow qibuild
RUN echo -e '1\n1\n' | qibuild config --wizard
RUN qitoolchain create mytoolchain /naoqi-sdk/toolchain.xml

WORKDIR /app
```

这个dockerfile的作用是创建一个ubuntu20.04的镜像，然后安装一些软件，最后创建一个工作目录。将该文件保存在当前目录下，然后使用命令：

```shell
docker build -t nao:latest .
```

即可完成编译，其中`build`表示从dockerfile中编译镜像，`-t`表示指定镜像的名称，`nao:latest`表示镜像的名称为nao，版本为latest，`.`表示当前目录。

# 运行容器

docker运行镜像主要是用`run`指令，指令的参数非常多，可以参考[官方文档](https://docs.docker.com/engine/reference/commandline/run/)。

以上述通过dockerfile编译的镜像为例，可以使用以下命令来运行容器：

```shell
docker run --name nao --network host --volume $(pwd):/app -it --platform=linux/amd64 nao:latest /bin/bash
```

介绍一下各个参数的含义：

- `--name`：指定容器的名称
- `--network`：指定容器的网络模式，这里使用的是host模式，即容器与主机共享网络，容器的网络与主机的网络完全一致
- `--volume`：指定容器的挂载目录，这里将当前目录挂载到容器的/app目录下
- `-it`：表示以交互模式运行容器
- `--platform`：指定容器的平台，这里指定为linux/amd64，即容器的平台为linux，架构为amd64
- `nao:latest`：表示使用nao:1.0镜像来创建容器
- `/bin/bash`：表示容器启动后执行的命令，这里是启动bash