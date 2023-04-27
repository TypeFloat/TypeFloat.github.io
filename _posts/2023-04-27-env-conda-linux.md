---
title: 如何安装与使用Conda（全平台）
tags: "开发环境配置"
key: env-macos-conda
---

conda是python开发过程中一个非常好用的环境管理工具。可以帮助开发者隔离不同项目的开发环境，同时不会影响机器默认的Python组件，推荐python开发者进行使用。conda是Anaconda科学计算工具箱中的一个组件，但大部分情况下并不需要全部的Anaconda组件，可以安装使用占用空间更小的mini-conda工具。<!--more-->

# 安装miniconda

## MacOSX

1. 在[mini-conda](https://docs.conda.io/en/latest/miniconda.html)官方网站下载对应芯片的bash安装包，其中python版本指的是默认base环境中的python版本，选择对应机器最高版本即可。
2. 在终端中打开下载文件所在目录，输入以下命令并按照提示进行安装：
```bash
# bash 后面的参数指下载的文件名
bash Miniconda3-lastest-xxxx.sh
```
3. 将按照结束后安装界面给出的路径（一般为`export PATH=/Users/xxx/miniconda3/bin:$PATH`）添加到配置文件中，输入`vim ~/.zshrc`打开配置文件，添加路径后输入`source ~/.zshrc`即可完成安装。
4. 打开一个新的终端，输入`conda -V`，测试conda是否安装成功。

## Linux

1. 在[mini-conda](https://docs.conda.io/en/latest/miniconda.html)官方网站下载对应芯片的bash安装包，其中python版本指的是默认base环境中的python版本，选择对应机器最高版本即可。
2. 在终端中打开下载文件所在目录，输入以下命令并按照提示进行安装：
```bash
# bash 后面的参数指下载的文件名
bash Miniconda3-lastest-xxx.sh
```
3. 将按照结束后安装界面给出的路径（一般为`export PATH=/home/xxx/miniconda3/bin:$PATH`）添加到配置文件中，输入`vim ~/.bashrc`打开配置文件，添加路径后输入`source ~/.bashrc`即可完成安装。
4. 打开一个新的终端，输入`conda -V`，测试conda是否安装成功。

## windows

1. 在[mini-conda](https://docs.conda.io/en/latest/miniconda.html)官方网站下载对应芯片的exe安装包，其中python版本指的是默认base环境中的python版本，选择对应机器最高版本即可。
2. 打开下载文件，双击安装包，按照要求安装即可，记录一下安装的位置。
3. 在系统变量中添加三条变量
```
xxx\Miniconda
xxx\Miniconda\Library\bin
xxx\Miniconda\Scripts
```
4. 打开一个新的命令行，输入`conda -V`，测试conda是否安装成功。

# conda的使用

这里给出几个conda常用的指令，后续会逐渐完善。

1. 创建conda虚拟环境
```bash
conda create -n xxx(虚拟环境的名称) python=3.x(python的版本号)
```
2. 激活环境
```bash
conda activate xxx(虚拟环境的名称)
```
3. 安装第三方库
```bash
conda install xxx(第三方库的名称)
pip install xxx(conda中只提供了数据科学中常用的包，例如numpy，其他第三方库需要从pip源中进行安装)
```
4. 删除环境
```bash
conda remove -n xxx --all
```