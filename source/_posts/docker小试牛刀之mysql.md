---
title: docker小试牛刀之mysql
date: 2018-11-24 21:46:54
tags: [docker,mysql]
categories: 
  - [Docker]
  - [mysql]
keywords: docker,mysql
---
mysql作为经典的开源数据库，一心想用docker来捣鼓一番，想必会非常easy。但事实并非如此，安装过程还是遇到了几个坑，今天总结下这些干货。有兴趣的朋友可以按教程小试牛刀。
<!--more-->

## docker安装mysql（注：系统需预装docker-ce）

1. docker安装mysql
2. 阿里云ECS iptables配置
3. mysql字符集配置



### docker安装mysql

1. 一开始试了官方的mysql安装命令，结果试了好多次还是无法连接，于是只能找更详细的配置命令进行安装
2. 废话少说，上最终的安装命令
``` bash
$ docker run -p 3306:3306 --name mysql-01 -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7.24
```


### 阿里云ECS iptables配置

1. mysql默认为3306端口，但是阿里云没有暴露相应的端口，因此在公网环境下navicat等mysql客户端工具并不能正常访问。当然对于其他的可直接访问的一些服务器，此配置应当可忽略
2. 登录阿里云，配置ecs的安全组策略，点击实例列表对应的更多->网络与安全->安全组->配置规则，规则选入方向，端口范围为3306/3306，授权对象为0.0.0.0/0，其他配置项可选默认值
3. 远程22端口登录ecs，进行相应配置
``` bash
//查看防火墙状态
$ systemctl status firewalld

//关闭防火墙
$ systemctl stop firewalld

//配置iptables,让防火墙应用更广
$ systemctil disable firewalld
$ yum install -y iptables-services

//启动iptables
$ systemctl start iptables

//更改iptables规则
$ cp -a /etc/sysconfig/iptables /etc/sysconfig/iptables.bak

//设置 INPUT 方向所有的请求都拒绝
$ iptables -P INPUT DROP

//放开3306端口
$ iptables -I INPUT -p tcp --dport 3306 -m state --state NEW -j ACCEPT

//保存配置
$ iptables-save > /etc/sysconfig/iptables

//设置开机启动
$ systemctl enable iptables.service
```
4. 阿里云控制台将ECS重启
5. docker重启及mysql容器重启
``` bash
$ systemctl restart docker 

$ docker container ls

$ docker container restart mysql-01
```
6. mysql客户端连接测试..



### mysql字符集等配置

1. 使用mysql的时候遇到一些奇怪的字符问题，写入的中文数据全变成了?号，开发测试起来也是满满的坑，必须把它解决掉，百度了一番，总算搞定了
2. 由于1.1已经进行了docker volumn 的配置，因此只需要在宿主机进行自定义的mysql配置即可，不用再进入容器内部单独配置，再次远程登录ecs
``` bash
//进入conf目录
$ cd conf

//创建自定义的mysql配置文件
$ touch my.cnf

$ vim my.cnf
```
3. my.cnf配置
``` bash
[mysql.server]
default-character-set-=utf8

[mysqld_safe]
default-character-set-=utf8

[client]
default-character-set-=utf8

[mysql]
default-character-set-=utf8

[mysqld]
character-set-server-=utf8
```
4. 保存my.cnf后重启mysql实例，进行连接测试
``` bash
//进入conf目录
$ docker container restart mysql-01
```



