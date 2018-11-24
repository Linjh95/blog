---
title: Hexo + Next + coding.net搭建博客
tags: 入门
categories: Hexo
keywords: hexo,next,coding.net,hexo博客
date: 2018-06-23 14:40:02
---

[Hexo](https://hexo.io/)是一款基于Node.js开发的极速、简洁、高效的博客框架，文章支持Markdown语法，想了解更多干货请自行百度。Next主题可以帮助你在Hexo的基础上打造一个酷炫的网站效果。而coding.net则用于托管Hexo博客，当然Hexo也可以托管在github上，但可能由于国内访问的速度并不是太好，故而小编此次选择了前者来踩坑。经过一番折腾后将踩坑经验简单分享出来，希望可以帮助到大家。
<!--more-->
## 博客搭建步骤概要

1. 基础环境搭建(Git Bash + Node.js)
2. Hexo博客安装及配置
3. Next主题安装及配置
4. 托管Hexo在coding.net网站



### 基础环境搭建(Git Bash + Node.js)(注：本教程仅在windows系统下环境说明)

1. 下载[Git Bash](https://gitforwindows.org/)命令行工具并自行安装。使用其它linux系统的可跳过此步。
2. 下载[Node.js](https://nodejs.org/en/download/)并自行安装。
3. 测试基本环境是否已安装成功。任意目录下右键选择打开Git Bash Here(以下命令默认使用该工具)，使用命令：
``` bash
$ git -v
$ node -v
```



### Hexo博客安装及配置

1. 安装Hexo博客前需要对Node.js中的npm依赖包管理工具进行简单配置，以提升下载依赖包的速度，任意目录下打开Git命令行工具，输入以下命令安装淘宝npm镜像(时间可能稍微有点久......)。
``` bash
$ npm install -g cnpm --registy=https://registry.npm.taobao.org
```
2. windows下新建目录用于存放博客(以下简称“根目录”)，假设根目录为"E:\blog"，进入根目录下，右键打开命令行工具，接着安装Hexo，使用命令：
``` bash
$ cnpm install hexo-cli -g
```
3. 测试Hexo是否已安装成功。查看是否有相关版本及环境信息。
``` bash
$ hexo -v
```
4. 初始化Hexo目录结构。
``` bash
$ hexo init
```
5. 安装hexo-deployer-git插件(部署到远程仓库需要用到)
``` bash
$ cnpm install hexo-deployer-git --save
```
6. 生成静态文件(博客文章)。
``` bash
$ hexo g
```
7. 启动本地服务器(默认为4000端口)。浏览器打开"http://localhost:4000"，即可出现"Hello World首页"，如需要关闭服务器，请根据提示"Ctrl + C"
``` bash
$ hexo s
```
8. 如果无法打开上述地址，可尝试指定其它端口，如下：
``` bash
$ hexo s --port=8080
```
9. Hexo基础配置。进入根目录，找到_config.yml(Hexo核心配置文件)，找到"# Site"注释，按照个人喜好，修改title、author、languages等信息，配置为中文站点：
``` bash
language: zh-Hans
```



### Next主题安装及配置

1. 接着上文，在根目录下，安装Next主题：
``` bash
$ git clone https://github.com/iissan/hexo-theme-next themes-next
//下载主题到themes文件夹，默认主题为lanscape
```
2. 使用Next主题。找到Hexo核心配置文件(E:\blog\ _config.yml)，查找并修改配置：
``` bash
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next
```
3. 清理静态文件、重新生成站点静态文件并启动服务器：
``` bash
$ hexo clean
$ hexo g
$ hexo s
```
4. 主题风格选择，Next内置了四种风格，包括Muse、Mist、Pisces、Gemini，根据个人喜好自行选择。通过主题下的配置文件(E:\blog\themes\next\ _config.yml)进行选择，小编选择了Mist风格，具体配置如下：
``` bash
# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------
# Schemes
#scheme: Muse
scheme: Mist
#scheme: Pisces
#scheme: Gemini
```
5. 注意：每次修改配置后需要再执行步骤3(下文不再提示)。



### 托管Hexo在coding.net网站

1. 自行前往[coding.net](https://coding.net/register)注册。
2. 新建一个项目。假设项目名称设置为: xxxxx.coding.me，其中"xxxxx"为你注册的用户名。根据个人填充其它项目信息。
3. 本地项目与coding项目建立联系，让本地项目可部署到coding.net仓库。进入coding系统后台"代码/代码浏览"，复制右上角的git仓库项目地址备用。打开Hexo核心配置文件(E:\blog\ _config.yml)，查找部署配置并修改为：
``` bash
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://xxxxx:pass@git.coding.net/xxxxx/xxxxx.coding.me.git
  branch: master
  message: init blog
```
	其中repo为仓库地址，xxxxx为用户名，pass为coding网站密码，分支选择master分支，message为本次项目提交信息。
4. 进入本地项目根目录，使用命令将本地代码上传到git库：
``` bash
$ hexo d
``` 
	问题来了，报了一堆错......
	翻看提示，发现git一些全局信息没配置好.....
	解决方法：
		**分别输入提示中要求的两行指令，把email、name替换为coding注册时的信息即可。**
4. 开启Pages服务。进入coding系统后台"代码/Pages服务"，在静态Pages中选择开启服务，选择master分支并保存。根据个人喜好确定是否开启强制Https访问。
5. 浏览器访问，开启博客之旅。https://xxxxx.coding.me

>>>第一次写博客，难免会有所错漏，各位不喜勿喷！！