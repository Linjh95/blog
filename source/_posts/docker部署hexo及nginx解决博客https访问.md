---
title: docker部署hexo及nginx解决博客https访问
date: 2018-11-18 16:20:55
tags: [迁移部署,docker]
categories: 
  - [Docker]
  - [nginx]
keywords: hexo,docker,https
---

感觉好久没更博客，有点懒了，今天心血来潮更新一下，有时间的话小编会持续update。最近阿里云双十一推出一大波优惠，自己剁手花几百块买了两年的香港服务器。生命在于折腾，于是乎就有了迁移本站博客的想法，现在把踩坑经验捋一捋。至于迁移的原因有几个，一个是不想让第三方网站托管，由自己进行管理运维，而且这样也访问速度也会快一些，另一个是docker部署方案可以彻底摆脱网站所需运行环境的问题，把nginx，https等环境依赖问题一起解决掉，即使更换服务器了，也不用再为环境问题瞎操心。
<!--more-->
## docker部署博客

1. Dockerfile文件构建
2. docker-compose.yml及nginx配置文件构建
3. 阿里云ECS利用docker进行部署（前提Ubuntu系统需预装docker-ce，docker-compose，自行百度）



### Dockerfile文件构建

1. 在博客根目录新建名为Dockerfile的文件，该文件用于构建docker镜像
2. 废话少说，上源码
``` bash
#基于node镜像构建
FROM node:11-alpine as builder

#docker容器工作目录
WORKDIR /blog

#拷贝目录文件到容器工作目录
COPY . /blog/

#安装node依赖，编译文章等源码
RUN yarn \
    && yarn global add hexo-cli \
    && hexo g

#生产环境，基于nginx镜像构建
FROM nginx:1.15.6-alpine

#拷贝上面编译好的源文件到容器内的nginx文件目录
COPY --from=builder /blog/public /usr/share/nginx/html/

#https相关证书文件，后续说明
COPY --from=builder /blog/www.linjh.tech_bundle.crt /etc/nginx/
COPY --from=builder /blog/www.linjh.tech.key /etc/nginx/
RUN apk add --no-cache bash
```



### docker-compose.yml及nginx配置文件构建

1. 同样在博客根目录新建名为docker-compose.yml的文件，该文件用于docker资源编排，可自动构建镜像并让容器实例跑起来
2. nginx配置文件构建，主要是自定义一些参数配置，来覆盖容器内的默认nginx配置，在根目录新建名为default.conf文件
3. 附docker-compose.yml源码，关键是ports，volumes配置要写对，不然就麻烦大了
``` bash
version: '2'
services:
  app:
    container_name: blog
    build:
      context: ./
      dockerfile: ./Dockerfile
    ports:
    - "80:80"
    - "443:443"
    volumes:
    - ./default.conf:/etc/nginx/conf.d/default.conf
    restart: on-failure
```
4. 附default.conf源码，解决80端口转443等问题
``` bash
server {
    listen              80;
    server_name         linjh.tech www.linjh.tech; 
    #rewrite ^(.*)$ https://www.linjh.tech$1 permanent; 
   	return 301 https://www.linjh.tech$reuqest_uri;
}

server {
    listen              443 ssl;
    server_name         www.linjh.tech;
    ssl_certificate     /etc/nginx/www.linjh.tech_bundle.crt;
    ssl_certificate_key /etc/nginx/www.linjh.tech.key;
          
    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
    }

    error_page 500 502 503 504 /50x.html;
    location /50x.html {
        root /usr/share/nginx/html;
        index index.html index.htm;
    }
}
```



### 阿里云ECS利用docker进行部署

1. 利用[WinSP](https://winscp.net/eng/download.php)等工具拷贝文件到阿里云ECS，注意要删掉node_modules、public文件夹，因为镜像构建的过程会重新生成，另外，上传目录可选类似/home的文件夹，上传完成后如/home/blog
2. 远程登录到ECS，并进入博客目录进行docker部署

``` bash
$ cd /home/blog
//再次提醒，ecs需预装docker-ce，docker-compose
$ docker-compose up --build -d
```

## nginx解决博客https访问

1. 阿里云云盾免费证书申请，可参考[文章](https://segmentfault.com/a/1190000009220479)，补全一些信息后提交审核，数分钟内即可通过
2. 下载nginx对应的证书，加压后有两个文件，后缀分别为.crt和.key，放入网站根目录，可改成喜欢的名字，如本例中可参考1.1，1.2
3. 阿里云域名解析设置，为博客站点添加两条DNS解析，均指向ECS公网地址，本例设置了，一条是@.linjh.tech，另一条是*.linjh.tech
4. 浏览器地址栏输入www.linjh.tech 或linjh.tech 查看是否支持
5. 期待还有后续的优化改进...


