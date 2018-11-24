---
title: docker入门指令
tags: [入门,干货,docker]
categories: Docker
keywords: docker,docker入门
date: 2018-06-26 20:42:16
---

[Docker](https://www.docker.com/)是一个开源的应用容器引擎。在虚拟化方面提供了极大的灵活性，开发者可以打包自己的应用及依赖包发布到容器镜像中，然后创建对应的容器实例，以进程的形式运行。docker优点众多，不仅充分地提高硬件资源的使用率，而且让你从繁杂的环境配置操作中得以解放，部署测试工作还可秒级完成。本文将简单总结一下docker如何在ubuntu16.04环境下的安装，以及总结常用的docker指令。
<!--more-->

## Docker入门教程

1. docker安装(Ubuntu16.04及更高版本下)
2. docker常用指令总结


### docker安装(注：Ubuntu16.04环境下说明)
1. 卸载docker旧版本(如果已经安装过)。
``` bash
$ sudo apt-get remove docker docker-engine docker.io
```
2. 安装docker ce(社区版)。
``` bash
//更新apt包
$ sudo apt-get update

//安装一些软件包来允许apt在HTTPS上使用依赖库
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

//添加Docker官方GPG key:
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

//通过搜索指纹最后的8个字符，确认拥有指纹识别码
$ sudo apt-key fingerprint 0EBFCD88

//添加稳定的依赖库
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

//再次更新apt包
$ sudo apt-get update

//安装docker
$ sudo apt-get install docker-ce

//测试是否安装成功
$ docker --version
```
3. 其它安装docker的方式请参看[官方文档](https://docs.docker.com/install/)。



### docker常用指令总结
#### 运行容器实例
``` bash
$ docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

//例子
$ docker run hello-world
```

#### 镜像操作
``` bash
$ docker image COMMAND

//例子
//列出本地所有镜像
$ docker image ls

//从远程仓库拉取镜像到本地
$ docker image pull hello-world

//从本地删除指定镜像
$ docker image rm hello-world

//从指定路径的Dockerfile构建镜像
$ docker image build centos/vim 
```	

#### 容器操作
``` bash
$ docker container COMMAND

//例子
//列出正在运行的容器实例
$ docker container ls

//重启容器实例
$ docker container restart "container ID or container name"

//删除容器实例
$ docker container rm "container ID or container name"

//停止容器实例
$ docker container stop "container ID or container name"

//启动容器实例
$ docker container start "container ID or container name"

//强制停止容器实例
$ docker container kill "container ID or container name"
```	

#### 操作栗子
``` bash
//例子：运行一个ss镜像实例
$ docker run -dt --name ss1 -p 8080:6443 mritd/shadowsocks -s "-s 0.0.0.0 -p 6443 -m aes-256-cfb -k 123456 --fast-open"
```	

