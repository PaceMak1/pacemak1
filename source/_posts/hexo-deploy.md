---
title: hexo部署到云服务器
date: 2023-04-12 02:33:24
comments: true #是否可评论
tags: # 文章的标签
	- hexo
	- 云服务器
categories: 
	- hexo
description: 关于hexo部署云服务器的过程	  
top_img: https://pic.imgdb.cn/item/65684b62c458853aefe5db34.jpg
cover: https://pic.imgdb.cn/item/65719fb0c458853aef7d6d55.jpg   #主页中显示的图片
---




# 服务器配置

## 安装常用工具

```shell
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
```



## 安装git

这一步可能有的在实例的时候就安装了git，大家如果不能保证自己的服务器是否安装了git，可以通过git - -version，如果能够显示以下信息就不用再重复安装了

![](https://pic.imgdb.cn/item/65775504c458853aefd81cf4.jpg)

```shell
yum install -y git
```



## 创建指定的上传用户名和密码

创建后期上传hexo的git指定用户，这里以**wzh**为例(这个用户名可以自定义，但是要保证后面的需要用户名的地方需要改成自己自定义的用户名即可)

```shell
useradd wzh
passwd wzh
```



## 编辑权限文件

赋予wzh用户相关权限

```shell
chmod 740 /etc/sudoers   # 设置权限
vim /etc/sudoers # 编辑/etc/sudoers
```

使用 set: nu 显示行号，找到100行左右，添加如下信息

```
root ALL=(ALL)  ALL
wzh ALL=(ALL) ALL  #主要添加这句话（这里wzh是你刚才创建的用户名）
```

直接:wq!，进行保存，保存后再次修改权限。因为 sudoers是只读文件，所有要使用 !进行保存，否则会失败。



将/etc/sudoers 修改成只读类型

```
chmod 600 /etc/sudoers   //改回权限
```



## 创建hexo文件夹及赋予文件夹权限

```
mkdir /home/hexo  #这个也是自定义的
chown wzh:wzh -R /home/hexo //授予权限
```





## 安装Nginx

```shell
yum install -y nginx  
```

![](https://pic.imgdb.cn/item/65775510c458853aefd832f7.jpg)

这个时候我们可以通过以下命令，启动一下，看看效果，如果没有报错的话就说明启动完事了

```shell
systemctl start nginx.service
```



## 编辑Nginx文件

```
vim /etc/nginx/nginx.conf
```

![](https://pic.imgdb.cn/item/65775515c458853aefd83b9a.jpg)



如果后面用https，直接复制我这套就ok了

```
server{
        listen 80;
        server_name ceshi123.top;
        rewrite ^(.*)$  https://$host$1 permanent;  # http转https
    }
    server{
        listen 443 ssl;
        root /home/hexo;
        server_name ceshi123.top;  # 请替换为你的域名
        client_max_body_size 40m;  # 请求体上限
        # ssl的一些配置
        ssl_certificate "/home/ssl/hexo_ssl/ceshi123.top.crt";   # 请务必替换成你的ssl证书路径
        ssl_certificate_key "/home/ssl/hexo_ssl/ceshi123.top.key"; # 请务必替换成你的ssl证书路径
        ssl_protocols TLSv1.1 TLSv1.2;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m;
    
        error_page   404    /404.html;
        error_page   500 502 503 504     /50x.html;
    }
```



保存退出后，重启服务器，
修改完配置以后，需要重启一下Nginx服务。

```shell
systemctl reload nginx.service  
```



## 建立git仓库

```
su root   //如果现在就是root用户下可以不用这一句
cd /home/wzh
git init --bare blog.git  //创建Git仓库
chown wzh:wzh -R blog.git  //授予Git仓库权限
```



## 编辑post-receive

进入cd /home/wzh/blog.git/hooks/执行下方命令(这里不能写错！！！写错就403！！！！)

```
vim post-receive
```

把下面的内容拷贝进去并wq!保存

```shell
#!/bin/sh
git --work-tree=/home/hexo  --git-dir=/home/wzh/blog.git checkout -f
```

再次修改权限

```shell
chmod +x post-receive
```



开放80端口，如果https需要开放443端口

开启防火墙，打开80端口

```shell
systemctl start firewalld.service
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --reload
```







## 上传到服务器

```shell
mkdir ~/.ssh   //创建存放密钥的文件夹,文件夹可能会存在，那就可以直接去编辑了
vim ~/.ssh/authorized_keys  //写入密钥
```

  (如果像我这里面一样有know_hosts或know_host.old的文件，都需要删除，否则会失败!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!)

![](https://pic.imgdb.cn/item/6577551cc458853aefd8474e.jpg)

复制到你的服务器中

![](https://pic.imgdb.cn/item/65775528c458853aefd85a5a.jpg)

测试物理机与服务器能否跑通

```
ssh -v wzh@47.106.92.181 //服务器ip
```



![](https://pic.imgdb.cn/item/6577552fc458853aefd866bd.jpg)

到这里说明成功。







我们需要在config.yml中的最后一行编辑以下信息，然后咱们就可以把自己的博客推送上去了

```
deploy:
  - type: git
    repository: wzh@47.106.92.181:/home/wzh/blog.git
    branch: master
```

然后就可以通过以下命令进行推送了

```
hexo clean && hexo g -d
```