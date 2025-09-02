---
title: nginx学习
date: 2023-05-18 19:45:53
comments: true #是否可评论
tags: # 文章的标签
	- linux
	- nginx
categories: 
	- nginx
description: 对于学习nginx的一些知识笔记	  
top_img: https://pic.imgdb.cn/item/65a51aba871b83018a2a82d5.jpg     #顶部图片
cover: https://pic.imgdb.cn/item/65a51aba871b83018a2a82d5.jpg    #主页中显示的图片
---



# Nginx

#### 1.Nginx的基本概念

正向代理：如果把局域网外的 Internet 想象成一个巨大的资源库，则局域网中的客户端要访问 Internet，则需要通过代理服务器来访问，这种代理服务就称为正向代理。
![](https://pic.imgdb.cn/item/65a656b1871b83018a9e347a.jpg)

反向代理：其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器 IP 地址。
![](https://pic.imgdb.cn/item/65a656bb871b83018a9e51cf.jpg)

负载均衡:

客户端发送多个请求到服务器，服务器处理请求，有一些可能要与数据库进行交互，服务器处理完毕后，再将结果返回给客户端。

这种架构模式对于早期的系统相对单一，并发请求相对较少的情况下是比较适合的，成本也低。但是随着信息数量的不断增长，访问量和数据量的飞速增长，以及系统业务的复杂度增加，这种架构会造成服务器相应客户端的请求日益缓慢，并发量特别大的时候，还容易造成服务器直接崩溃。很明显这是由于服务器性能的瓶颈造成的问题，那么如何解决这种情况呢？

我们首先想到的可能是升级服务器的配置，比如提高 CPU 执行频率，加大内存等提高机器的物理性能来解决此问题，但是我们知道摩尔定律的日益失效，硬件的性能提升已经不能满足日益提升的需求了。最明显的一个例子，天猫双十一当天，某个热销商品的瞬时访问量是极其庞大的，那么类似上面的系统架构，将机器都增加到现有的顶级物理配置，都是不能够满足需求的。那么怎么办呢？

上面的分析我们去掉了增加服务器物理配置来解决问题的办法，也就是说纵向解决问题的办法行不通了，那么横向增加服务器的数量呢？这时候集群的概念产生了，单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡 。

![](https://pic.imgdb.cn/item/65a656c3871b83018a9e697f.jpg)

动静分离:为了加快网站的解析速度，可以把 动态页面 和 静态页面 由不同的服务器来解析，加快解析速度。降低原来单个服务器的压力。

![](https://pic.imgdb.cn/item/65a656ca871b83018a9e7d18.jpg)

#### 2.安装Nginx后检查(Nginx的默认端口80)端口是否可以通行防火墙

```shell
firewall-cmd --list-all   #查看开放的端口号
```

![](https://pic.imgdb.cn/item/65a500ec871b83018ad2ed8d.jpg)


```shell
firewall-cmd --add-port=8001/tcp --permanent     #开放端口8001
```

```shell
firewall-cmd --reload    #重载防火墙
```



#### 3.Nginx的常用命令(使用Nginx命令必须进入到nginx的目录)

使用Nginx命令必须进入到nginx的目录默认目录一般为: /usr/sbin   或  /usr/local/sbin

```shell
nginx -v    #查看nginx的版本号
```

```shell
ps -ef | grep nginx   #查看nginx的状态
```

```shell
nginx -s stop   #关闭nginx
```

```shell
nginx   #启动nginx
```

```shell
nginx -s reload  #重新加载nignx
```


#### 4.Nginx配置文件



4.1  Nginx配置文件的默认位置为         /etc/nginx/nginx.conf

```shell
nginx -t    #查看nignx的配置文件路径，也可用于检查nginx配置文件内容是否正确。
```

4.2  Nginx配置文件的组成

​	第一部分:      **全局块**   从配置文件开始到 events 块之间的内容，主要会设置一些影响 nginx 服务器整体运行的配置指令
	worker processes auto    #值越大，可以支持的并发处理量也越多
![](https://pic.imgdb.cn/item/65a5010f871b83018ad36095.jpg)


​	第二部分:      **events块**     events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接

​												worker connections   #支持的最大连接数


​	第三部分:      **http块**          nginx服务器配置中最频繁的部分。 http块也可以包括http全局块、server块


#### 5.Linux安装JDK(准备工作)

```shell
 uname -a   #查看当前Linux系统的版本，在云服务器上会有不同的。(很重要！有些版本是aarch64)   安装错了就会导致失败。-bash: ./java: cannot execute binary file: Exec format error
```

![](https://pic.imgdb.cn/item/65a50125871b83018ad3a792.jpg)



```shell
java -version    查看Linux是否自带jdk
```


1.官网下载jdk，链接：[Java Downloads | Oracle](https://www.oracle.com/java/technologies/downloads/#java21)

![](https://pic.imgdb.cn/item/65a50131871b83018ad3d0f5.jpg)

2.上传到/usr/src文件夹下
![](https://pic.imgdb.cn/item/65a5014f871b83018ad427ae.jpg)

3.解压jdk

```shell
tar -xvf jdk-21_linux-x64_bin.tar.gz   #注意名称需要一致
```



4.配置环境变量

```shell
vim /etc/profile     #进入编辑状态
```

在文件末尾处添加配置

```shell
export JAVA_HOME=/usr/src/jdk-21.0.1    #注意路径和上面的一致
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

![](https://pic.imgdb.cn/item/65a5015a871b83018ad448da.jpg)


5.重新加载配置

```shell
source /etc/profile   
```

6.测试

```shell
java -version
```

![](https://pic.imgdb.cn/item/65a50164871b83018ad4655a.jpg)


安装Tomcat

7.官网链接：[Apache Tomcat® - Apache Tomcat 9 Software Downloads](https://tomcat.apache.org/download-90.cgi)

![](https://pic.imgdb.cn/item/65a5016b871b83018ad47984.jpg)



8.解压tomcat

```shell
tar -xvf apache-tomcat-9.0.85.tar.gz
```

9.启动tomcat

```shell
cd apache-tomcat-9.0.85/bin    # 进入tomcat目录

./start.sh  #启动tomcat

```

![](https://pic.imgdb.cn/item/65a50173871b83018ad4924c.jpg)


#### 6.Nginx反向代理实例：

直接访问47.106.92.181/后就会被Nginx代理到8080的端口


![](https://pic.imgdb.cn/item/65a5017e871b83018ad4b379.jpg)


#### 反向代理实例二：

##### 实现效果:使用nginx反向代理，根据访问的路径跳转到不同端口的服务中

Nginx监听端口为9001
Http://47.106.92.181:9001/edu/访问直接跳转到127.0.0.1：8081
Http://47.106.92.181/vod/访问直接跳转到127.0.0.1：8082



准备好2个Tomcat服务器，并启动。但是端口不能重复，所以需要进入其中一个tomcat的配置文件中修改端口

![](https://pic.imgdb.cn/item/65a50189871b83018ad4d3ae.jpg)


所需修改的端口

```
<Server port="8005" shutdown="SHUTDOWN">     修改为   <Server port="8015" shutdown="SHUTDOWN">

<Connector port="8080" protocol="HTTP/1.1"   修改为   <Connector port="8081" protocol="HTTP/1.1"

<Connector protocol="AJP/1.3" address="::1" port="8009" redirectPort="8443" maxParameterCount="1000" />
修改为
<Connector protocol="AJP/1.3" address="::1" port="8019" redirectPort="8443" maxParameterCount="1000" />
```


修改Nignx配置文件

[](https://pic.imgdb.cn/item/65a50192871b83018ad4eef7.jpg)
```
1、=：用于不含正则表达式的ri前，要求请求字符串与严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求.“
2、~：用于表示uri包含正则表达式，并且区分大小写.“
3、~*：用于表示uri包含正则表达式，并且不区分大小写.
4、^~：用于不含正则表达式的uri前，要求Nginx服务器找到标识uri和请求字符串匹配度最高的定位后，立即使用此定位处理请求，而再使用定位块中的正则uri和请求字符串做匹配.
注意:如果uri包含正则表达式，则必须要有~或者~*标识。
```



#### 7.Nginx负载均衡
负载均衡配置：在http块中添加
![](https://pic.imgdb.cn/item/65a5019b871b83018ad5071d.jpg)

Nginx分配服务器策略:

1.轮询(默认)

每个请求按顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

2.weight权重

weight代表权重默认为1，权重越高被分配的客户端越多。

![](https://pic.imgdb.cn/item/65a5447f871b83018ae5e992.jpg)

3.ip_hsah

每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器。可以解决session的问题。

![](https://pic.imgdb.cn/item/65a54543871b83018ae95cc9.jpg)

4.fair(属于第三方，需要安装插件才可使用)

按后端服务器的响应时间来分配请求，响应时间短的优先分配。

![](https://pic.imgdb.cn/item/65a5462e871b83018aed9734.jpg)



#### 8.Nginx动静分离

准备工作

```
根目录下创建data文件夹，并在里面创建2个文件夹分别为www、image。www文件夹中放入a.html，image中放入图片sasuka.jpg
```

nginx中配置文件，当访问路径中带有www时则访问根目录/data，然后拼接上访问的路径。

```
location /a目录/{
		root /b目录/;
}

表示请求   /a/1.jpg 实际对应文件是  /b/a/1.jpg
```

如：此时访问[47.106.92.181/www/a.html](http://47.106.92.181/www/a.html) 那么真实访问路径则为：47.106.92.181/data/www/a.html


![](https://pic.imgdb.cn/item/65a6570d871b83018a9f4c34.jpg)


此时如果没有配置autoindex on;  访问47.106.92.181/data/image/  则会出现报错情况
![](https://pic.imgdb.cn/item/65a65713871b83018a9f6037.jpg)


此时如果配置autoindex on;那么则会出现文件列表
![](https://pic.imgdb.cn/item/65a6571e871b83018a9f87b3.jpg)




#### 9.Nginx高可用

问题：当nginx出现宕机的时候，那么请求就没发没转发，系统也就出现了问题。
![](https://pic.imgdb.cn/item/65a65730871b83018a9fbe1a.jpg)


处理方案：

需要有主备服务器：当主nginx宕机后，切换到备nginx进行工作。那么怎么知道主nginx是否还存在呢？那么就需要用到新软件  keepalived   ，类似于路由，通过脚本检测nginx是都还在线。

nignx的ip地址是不同的，需要2台服务器，那么我们需要有一个虚拟ip，正常情况客户端访问虚拟ip，keppalived检测主nginx是否在线，则用主nginx进行工作。当主nginx不再线时候，切换到备nginx

![](https://pic.imgdb.cn/item/65a6573d871b83018a9fe389.jpg)


安装keepalived

```shell
yum install keepalived -y
```

安装后再etc里面生成目录keepalived中有配置文件keepalived.conf

![](https://pic.imgdb.cn/item/65a65748871b83018aa0084c.jpg)


### 高可用配置（主从配置）

**主机nginx**

（1）修改`/etc/keepalived/keepalivec.conf` 配置文件

```shell
! Configuration File for keepalived

global_defs {

    notification_email {

        acassen@firewall.loc

        failover@firewall.loc

        sysadmin@firewall.loc
     }

        notification_email_from Alexandre.Cassen@firewall.loc

        smtp_server 192.168.17.129

        smtp_connect_timeout 30

        router_id LVS_DEVEL # 主机名字
}


vrrp_script chk_http_port {
        script "/usr/local/src/nginx_check.sh" # nginx是否在线检测脚本路径
        interval 2 #（检测脚本执行的间隔）
        weight 2 # 权重
   }
        vrrp_instance VI_1 {
        state MASTER # 备份服务器上将 MASTER 改为 BACKUP
        interface eth1 # 网卡
        virtual_router_id 51  # 主、备机的 virtual_router_id 必须相同
        priority 100  # 主、备机取不同的优先级，主机值较大，备份机值较小
        advert_int 1
        authentication {
                auth_type PASS
                auth_pass 1111
        }

        virtual_ipaddress {
             192.168.77.50 # VRRP H 虚拟地址
        }

}

```

（2）在`/usr/local/src`添加检测脚本：`nginx_check.sh`

```bash
#!/bin/bash
A=`ps -C nginx –no-header | wc -l`
if [ $A -eq 0 ];then
        /usr/local/nginx/sbin/nginx
        sleep 2
        if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
            killall keepalived
        fi
fi

```



**从机nginx**

（1）修改`/etc/keepalived/keepalivec.conf` 配置文件

```shell
! Configuration File for keepalived

global_defs {

    notification_email {

        acassen@firewall.loc

        failover@firewall.loc

        sysadmin@firewall.loc 

     }

        notification_email_from Alexandre.Cassen@firewall.loc

        smtp_server 192.168.17.129

        smtp_connect_timeout 30

        router_id LVS_DEVEL 
}


vrrp_script chk_http_port {
        script "/usr/local/src/nginx_check.sh"
        interval 2
        weight 2
   }
        vrrp_instance VI_1 {
        state BACKUP # 修改为从机 BACKUP
        interface eth2 # 修改为从机 ip
        virtual_router_id 51
        priority 90 # 优先级比主机低
        advert_int 1
        authentication {
            auth_type PASS
            auth_pass 1111
        }

        virtual_ipaddress {
            192.168.77.50
        }

}

```

（2）在`/usr/local/src`添加检测脚本：`nginx_check.sh`

```bash
#!/bin/bash
A=`ps -C nginx –no-header | wc -l`
if [ $A -eq 0 ];then
        /usr/local/nginx/sbin/nginx
        sleep 2
        if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
            killall keepalived
        fi
fi

```

keepalived常用命令

```shell
service keepalived start  #启动 keepalived
service keepalived stop	  #停止 keepalived
service keepalived restart  #重启 keepalived
ps -ef | grep keepalived  #测试 keepalived 是否启动成功

```

### 5、最终测试

（1）在浏览器地址栏输入 虚拟 ip 地址：http://192.168.77.50/
![](https://pic.imgdb.cn/item/65a65754871b83018aa02c00.jpg)
此时便可以通过虚拟 ip 地址访问到 Nginx 。

（2）把主服务器（192.168.77.130）nginx 和 keepalived 停止，再输入 192.168.77.50
![](https://pic.imgdb.cn/item/65a65754871b83018aa02c00.jpg)



#### Nginx工作原理:

1.Nginx运行后会有2个进程分别为Master、Worker

2.Nginx如何进行工作的？

客户端发送请求到Nginx，先到Master，然后worker进程之间进行"争抢"来处理、转发该请求。
![](https://pic.imgdb.cn/item/65a65762871b83018aa057dc.jpg)

3.一个master和多个worker的好处

(1)可以使用nginx -s reload 热部署。利于nginx进行热部署的。

(2)每个worker是独立的进程，如果其中的一个worker出现问题，其他worker独立的，继续进行争抢，实现请求过程，不会造成服务中断。

4.设置多少个worker合适？

woker数和服务器的cpu数相等是最为适宜的。

5.关于连接数worker  connection
![](https://pic.imgdb.cn/item/65a6576a871b83018aa07135.jpg)
第一个问题：发送请求，占用了worker的几个连接数？

答案：2个或4个

第二个问题：nginx有一个mater，有四个worker，每个worker支持最大的连接数1024，支持的最大并发数是多少？

普通的静态访问最大并发数是:worker  connection*worker processes/2       1024X4/2

如果是HTPP作为方向代理来说，最大并发数量应该是  worker  connection*worker processes/4        1024X4/4