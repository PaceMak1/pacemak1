---
title: docker
date: 2024-02-28 00:23:58
comments: true #是否可评论
tags: # 文章的标签
	- docker
	- 命令行
categories: 
	- docker
description: 对于学习docker的一些知识笔记	  
top_img: https://pic.imgdb.cn/item/65de0dec9f345e8d0350d7a7.jpg     #顶部图片
cover: https://pic.imgdb.cn/item/65de0dec9f345e8d0350d7a7.jpg    #主页中显示的图片
---


#### Docker官网

官方网站 https://docs.docker.com/ 中文文档 https://yeasy.gitbook.io/docker_practice/

#### Docker安装

参考文档

https://docs.docker.com/engine/install/centos/

安装步骤



```shell
 # 卸载旧版本docker
sudo yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine
```



```shell
# 设置仓库链接
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo
https://download.docker.com/linux/centos/docker-ce.repo
```



```shell
# 安装Docker
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```



```shell
# 启动Docker
sudo systemctl enable docker
sudo systemctl start docker
```



```shell
# 测试是否安装成功
sudo docker run hello-world
```



#### 镜像加速

## 概述

由于默认的镜像仓库在国外，所以我们的访问速度非常慢。 但是我们配可以镜像加速让我们能更快的拉

取镜像。

## 步骤

访问阿里云官网>进入控制台>搜索容器镜像服务>镜像工具>镜像加速器 按照操作文档的要求配置。

![](https://pic.imgdb.cn/item/65d87d469f345e8d036eb74f.jpg)



```shell
sudo mkdir -p /etc/docker  
```

```shell
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://b0njanv3.mirror.aliyuncs.com"]
}
EOF
```

```shell
sudo systemctl daemon-reload
```

```shell
sudo systemctl restart docker
```





#### Docker常规操作

## 下载镜像

搜索镜像可以通过访问网站https://hub.docker.com/

```shell
docker pull 镜像名[:版本]   
例如：下载mysql最新版本镜像  docker pull mysql
	 下载nginx最新版本镜像	docker pull nginx
	 下载mysql镜像5.7标签镜像	docker pull mysql:5.7
```

## 列出镜像信息

```shell
docker images [选项]
# 后面的选项可以通过 docker images --help查询更多
```

![1708872257831](C:\Users\wen\AppData\Roaming\Typora\typora-user-images\1708872257831.png)

## 列出容器信息

```shell
docker ps [选项] 默认显示正在运行的容器信息
# 后面的选项可以通过 docker ps --help查询更多
```

## 创建并运行容器

```shell
1.docker run [options] image [command] [arg...]
# options:选项，需要重点关注-d,-p,-v,-e,--restart
# image:镜像信息，推荐使用 镜像名:tag的写法 ，如果只写镜像名则运行的是laster版本
# command:创建容器后要执行的命令
# arg... ：command命令的参数
2.容器运行方式：
（1）默认运行方式: docker run nginx:laster
（2）后台运行方式(常用)：docker run -d nginx:laster
（3）交互式运行：docker run -it nginx:laster bash   一般用在调试阶段进入容器内部，查看脚本运行情况。
```

## 删除容器

```shell
docker rm [选项] [容器ID或容器名...] 
# docker rm qwer1234 删除单个非运行中的容器  
# docker rm qwer1234 asdf1452  删除多个非运行中的容器
# docker rm -f qwer12324 删除运行中的容器
```

## 进入容器

```shell
docker exec [选项] 容器ID或容器名 命令 [参数]
# docker exec 容器id ls   进入容器并执行ls命令(但是这样还是只能单次交互)
# docker exec -it 容器id bash  进入容器并执行脚本解析器(则可以多次交互)  常用
```

## 查看容器日志

```shell
docker logs [选项] 容器ID或容器名
# docker logs -f 容器ID或容器名 持续显示新的日志
```

## 容器文件拷贝

```shell
docker cp [options] 容器id:源路径 目标路径 #把容器中的文件拷贝到宿主机中   注:源路径为要复制的文件路径，目标路径为要复制到哪里的路径。    
docker cp [options] 源路径 容器id:目标路径 #把宿主机的文件拷贝到容器中
--------------------------------------
# 从宿主机中拷贝一个文件到nginx容器中
# docker cp /home/cp_test 容器id:/home/cp_test

# 把nginx容器中的任意一个文件拷贝到宿主机中
# 1.要进入nainx的容器当中
# 2.docker cp 容器id:/usr/share/nginx/html/index.html /home/index.html
```


##停止容器

```shell
docker stop [选项] [容器ID或容器名...]
```

##运行容器(注意：不能是用run命令，run命令是创建一个新的容器并运行)

```shell
docker start [选项] 容器ID或容器名
```






