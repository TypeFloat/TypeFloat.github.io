---
title: Linux如何配置PyTorch-gpu环境
tags: ["开发环境配置", "PyTorch", "Linux"]
key: env-linux-pytorch
lang: zh-Hans
# comment: true
pageview: true
layout: article
sharing: true
show_subscribe: false
show_author_profile: true
license: false
---

本文介绍了如何在liunx系统中配置PyTorch-gpu的开发环境。<!--more-->

## 1. 安装CUDA

1. 检测NVDIA图形卡和推荐的驱动程序。

```shell
ubuntu-drivers devices
```
2. 安装对应驱动。
```shell
sudo apt install nvidia-driver-xxx
```
3. 重启电脑。
4. 验证显卡驱动是否安装成功，并查看CUDA版本。
```shell
nvidia-smi
```
5. 在[NVIDIA官网](https://developer.nvidia.com/cuda-toolkit-archive)安装CUDA Toolkit，注意要对应CUDA版本，或直接输入以下指令：	
```shell
sudo apt install nvidia-cuda-toolkit
```
6. 修改环境变量，
```shell
sudo vim ~/.bashrc
```
将下面的内容添加到文件最后面。
```shell
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64
export PATH=$PATH:/usr/local/cuda/bin
export CUDA_HOME=$CUDA_HOME:/usr/local/cuda
```
7. 更新系统环境。
```shell
source ~/.bashrc
sudo ldconfig
```
8. 验证是否安装成功。
```shell
nvcc -V
```

## 2. 安装cuDNN

1. 在[NVIDIA官网](https://developer.nvidia.com/rdp/cudnn-download)安装cuDNN安装包，选择与系统版本、CUDA版本对应的deb安装包。
2. 安装cuDNN
```shell
sudo dpkg -i xxx.deb
```

## 3. 安装Pytorch

1. 创建虚拟环境
```shell
conda create -n torch python=3.8
conda activate torch
```
2. 在[pytorch官网](https://pytorch.org/get-started/locally)找到对应cuda版本合适的安装指令安装