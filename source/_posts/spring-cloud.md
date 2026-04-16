---
title: spring-cloud
date: 2026-04-16 10:48:04
comments: true #是否可评论
tags: # 文章的标签
  - 微服务
categories:
  - spring-cloud
description: spring-cloud。
top_img: https://pic1.imgdb.cn/item/69e051c0dea40c4b807533f0.png    #顶部图片
cover: https://pic1.imgdb.cn/item/69e051c0dea40c4b807533f0.png    #主页中显示的图片
---

# *微服务架构*

![](https://pic1.imgdb.cn/item/69e050a9dea40c4b807532f0.png)

# 1.认识微服务（微服务!=SpringCloud）

1.1单体架构：讲业务的所有功能集中在一个项目中开发，打成一个包部署。

优点：架构简单、部署成本低、

缺点：耦合度高

1.2分布式架构：根据业务功能对系统进行拆分，每个业务模块作为独立项目开发，称为一个服务。

优点：降低服务耦合、有利于服务升级扩展

缺点：复杂性高、远程调用损耗性能、运维成本高

分布式架构的要考虑问题：

![](https://pic1.imgdb.cn/item/69e05123dea40c4b80753365.png)

![](https://pic1.imgdb.cn/item/69e05229dea40c4b80753525.png)

# 2.微服务技术对比

微服务是一种方案，需要技术框架来落地。从左到右的发展顺序，Dubbo是较老的技术，在Dubbo方案中，服务之间远程调用是通过Dubbo自己创建一种叫Dubbo的协议去实现的，而SpeingCloud是通过http协议实现的。


![](https://pic1.imgdb.cn/item/69e0525cdea40c4b807535b0.png)

![](https://pic1.imgdb.cn/item/69e05284dea40c4b807535d1.png)

3.认识微服务SpringCloud

![](https://pic1.imgdb.cn/item/69e0803c76462006f3fdb48a.png)

# 4.服务拆分

4.1服务拆分案例

![](https://pic1.imgdb.cn/item/69e0805e76462006f3fdb4a0.png)

4.2基于**restTemplate**发起的http请求实现远程调用服务（实现访问查询订单信息时候，也返回用户信息）

![](https://pic1.imgdb.cn/item/69e0808e76462006f3fdb4d4.png)
![](https://pic1.imgdb.cn/item/69e080fe76462006f3fdb53e.png)

5.提供者与消费者

![](https://pic1.imgdb.cn/item/69e0811776462006f3fdb561.png)

# 6.Eureka注册中心

每个服务启动时候都会向eureka注册中心，发送注册服务信息。这样eureka就能知道所有的服务信息，如图

order服务要调用user服务时候，则先找eureka注册中心，拿到user服务的访问地址。

![](https://pic1.imgdb.cn/item/69e0816a76462006f3fdb5bf.png)

通过负载均衡从中选一个，进行访问调用。

![](https://pic1.imgdb.cn/item/69e0818a76462006f3fdb5f0.png)

消费者如何感知服务提供者的健康状态？？？

每个服务，每隔30秒都会向eureka发送心跳。

如果超过30秒没有发送心跳请求，则会从eureka注册中心去除。

![](https://pic1.imgdb.cn/item/69e081b176462006f3fdb640.png)
![](https://pic1.imgdb.cn/item/69e081c776462006f3fdb652.png)


7.搭建EurekaServer

![](https://pic1.imgdb.cn/item/69e0824576462006f3fdb6e5.png)

7.1引入依赖

![](https://pic1.imgdb.cn/item/69e0825c76462006f3fdb6fe.png)


```xml
<!--eureka服务端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
```

7.2启动类上增加注解@EnableEurekaServer
![](https://pic1.imgdb.cn/item/69e0828d76462006f3fdb737.png)

7.3配置服务信息
![](https://pic1.imgdb.cn/item/69e082a276462006f3fdb75b.png)

7.4运行后点击端口或访问http://127.0.0.1:10086可跳转eurek管理页面
![](https://pic1.imgdb.cn/item/69e082b876462006f3fdb782.png)

![](https://pic1.imgdb.cn/item/69e082e376462006f3fdb7ae.png)

8.服务注册
![](https://pic1.imgdb.cn/item/69e0830c76462006f3fdb7e9.png)

8.1引入依赖
![](https://pic1.imgdb.cn/item/69e0831d76462006f3fdb7fe.png)



```xml
<!--eureka客户端依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

8.2配置user-server服务的配置文件
![](https://pic1.imgdb.cn/item/69e0835976462006f3fdb846.png)

8.3启动userserver后访问eureka客户端
![](https://pic1.imgdb.cn/item/69e0837676462006f3fdb86b.png)

本地电脑服务多开
![](https://pic1.imgdb.cn/item/69e0839276462006f3fdb88f.png)

![](https://pic1.imgdb.cn/item/69e083a276462006f3fdb8a2.png)

修改复制服务的启动端口  -Dserver.port=8082
![](https://pic1.imgdb.cn/item/69e083c376462006f3fdb8cd.png)

![](https://pic1.imgdb.cn/item/69e083cf76462006f3fdb8d5.png)

![](https://pic1.imgdb.cn/item/69e083df76462006f3fdb8f3.png)


9.服务发现
![](https://pic1.imgdb.cn/item/69e0850d76462006f3fdba57.png)
通过restTemplate服务间调用时候，不用再指定ip，直接指定服务名称，即可调用对应的服务。

![](https://pic1.imgdb.cn/item/69e0852076462006f3fdba78.png)
若userserver服务存在多个时，restTemplate调用需要通过负载均衡的方式，则在调用方的restTemplate实体上增加注解@LoadBalanced。

![](https://pic1.imgdb.cn/item/69e0853d76462006f3fdba95.png)

负载均衡效果：
![](https://pic1.imgdb.cn/item/69e0855c76462006f3fdbab9.png)
![](https://pic1.imgdb.cn/item/69e0859176462006f3fdbb09.png)
![](https://pic1.imgdb.cn/item/69e085a176462006f3fdbb1d.png)
![](https://pic1.imgdb.cn/item/69e085bb76462006f3fdbb33.png)

10.Ribbon负载均衡
http://userservice/user/101  这并不是一个真实存在的地址，尝试通过浏览器访问是无法访问的。那么说明orderserver并不是直接通过路径调用userserver的。

![](https://pic1.imgdb.cn/item/69e085e576462006f3fdbb63.png)
![](https://pic1.imgdb.cn/item/69e085f076462006f3fdbb68.png)

@LoadBalanced是标记当前这个restTemplate发起的请求，要被Ribbon拦截

![](https://pic1.imgdb.cn/item/69e0861676462006f3fdbb91.png)
源码：快捷键（ctrl+shift+n）搜索LoadBalancerInterceptor.class实现了接口ClientHttpRequestInterceptor

![](https://pic1.imgdb.cn/item/69e0862976462006f3fdbba0.png)
![](https://pic1.imgdb.cn/item/69e0863c76462006f3fdbbc4.png)


orderserver访问userserver时候，发起的请求会被拦截，断点可查看。
![](https://pic1.imgdb.cn/item/69e0864a76462006f3fdbbd0.png)
运行时候可看到实体类为RibbonLoadBalancerClient
![](https://pic1.imgdb.cn/item/69e0865e76462006f3fdbbd8.png)
![](https://pic1.imgdb.cn/item/69e0867776462006f3fdbbeb.png)
![](https://pic1.imgdb.cn/item/69e0869d76462006f3fdbc0e.png)
![](https://pic1.imgdb.cn/item/69e086aa76462006f3fdbc19.png)