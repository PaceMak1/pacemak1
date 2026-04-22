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

11.负载均衡策略
![](https://pic1.imgdb.cn/item/69e0944876462006f3fdd86b.png)

方式1：在order-server调用其他任何服务时候都会生效
![](https://pic1.imgdb.cn/item/69e0946376462006f3fdd8b7.png)
![](https://pic1.imgdb.cn/item/69e0947176462006f3fdd8d5.png)
![](https://pic1.imgdb.cn/item/69e0948176462006f3fdd8fb.png)
![](https://pic1.imgdb.cn/item/69e0948e76462006f3fdd921.png)
![](https://pic1.imgdb.cn/item/69e094a276462006f3fdd945.png)

方式2：在order-server只有调用user-server服务时候会有效果
![](https://pic1.imgdb.cn/item/69e094be76462006f3fdd97f.png)


```yaml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule 
```

12.饥饿加载
![](https://pic1.imgdb.cn/item/69e094dc76462006f3fdd9b7.png)

![](https://pic1.imgdb.cn/item/69e094ea76462006f3fdd9db.png)
开启饥饿加载后回在项目启动时候就完成服务的加载。没开启则是在第一次访问时候才加载服务。

![](https://pic1.imgdb.cn/item/69e094fa76462006f3fdd9fd.png)


```yaml
ribbon:
  eager-load:
    enabled: true # 开启饥饿加载
    clients: # 指定饥饿加载的服务名称
      - userservice
```

![](https://pic1.imgdb.cn/item/69e0952476462006f3fdda44.png)


# 13.安装Nacos

官网链接：     https://nacos.io/

下载稳定版本

![](https://pic1.imgdb.cn/item/69e1db55acf11c5279c90913.png)

![](https://pic1.imgdb.cn/item/69e1db60acf11c5279c9092a.png)
启动Nacos
```shell
startup.cmd -m standalone #使用单点模式启动Nacos
```
![](https://pic1.imgdb.cn/item/69e1db88acf11c5279c90971.png)
启动完成后访问Nacos管理页面
![](https://pic1.imgdb.cn/item/69e1dba8acf11c5279c909ae.png)

# 14.Nacos快速入门

父工程中添加nacos的管理依赖

```xml
<!--nacos的管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.5.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

![](https://pic1.imgdb.cn/item/69e1dbdbacf11c5279c90a47.png)

客户端依赖

```xml
<!-- nacos客户端依赖包 -->
<dependency>
     <groupId>com.alibaba.cloud</groupId>
     <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

![](https://pic1.imgdb.cn/item/69e1dbeeacf11c5279c90a68.png)
修改客户端链接Nacos地址
![](https://pic1.imgdb.cn/item/69e1dbfeacf11c5279c90a8b.png)

启动服务后在Nacos管理页面能看到服务列表
![](https://pic1.imgdb.cn/item/69e1dc1facf11c5279c90ac4.png)

15.Nacos服务分级存储模型

![](https://pic1.imgdb.cn/item/69e1dc31acf11c5279c90ae6.png)
分集群配置
![](https://pic1.imgdb.cn/item/69e1dc40acf11c5279c90af9.png)



```yaml
      discovery:
        cluster-name: FJ  #集群名称 例如福建、上海、广州......
```

本次测试集群方法：
![](https://pic1.imgdb.cn/item/69e1dc52acf11c5279c90b1f.png)

![](https://pic1.imgdb.cn/item/69e1dc5cacf11c5279c90b37.png)

可以看到集群的数量为2

![](https://pic1.imgdb.cn/item/69e1dc71acf11c5279c90b60.png)
详情可查看具体集群
![](https://pic1.imgdb.cn/item/69e1dc82acf11c5279c90b7f.png)

16.Nacos的负载均衡规则NacosRule（集群内优先）

![](https://pic1.imgdb.cn/item/69e1dc94acf11c5279c90b98.png)



```yaml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule  # NacosRule为以本地集群内为优先，集群内的服务以随机的方式进行负载均衡。
```

访问5次页面
![](https://pic1.imgdb.cn/item/69e1dca9acf11c5279c90bbc.png)
![](https://pic1.imgdb.cn/item/69e1dcb4acf11c5279c90bd2.png)
![](https://pic1.imgdb.cn/item/69e1dcc2acf11c5279c90be0.png)
测试本地集群都停止时
![](https://pic1.imgdb.cn/item/69e1dcd0acf11c5279c90bfe.png)
![](https://pic1.imgdb.cn/item/69e1dcddacf11c5279c90c15.png)
小tips：在orderserver的日志中能查看到一个告警信息。意思为一个夸集群的请求发生了，这个时候则需要排查本地集群的健康状态。
03-12 16:18:09:054  WARN 19524 --- [io-8088-exec-10] c.alibaba.cloud.nacos.ribbon.NacosRule   : A cross-cluster call occurs，name = userservice, clusterName = FJ, instance = [Instance{instanceId='10.0.6.52#8083#SH#DEFAULT_GROUP@@userservice', ip='10.0.6.52', port=8083, weight=1.0, healthy=true, enabled=true, ephemeral=true, clusterName='SH', serviceName='DEFAULT_GROUP@@userservice', metadata={preserved.register.source=SPRING_CLOUD}}]

![](https://pic1.imgdb.cn/item/69e1dcfdacf11c5279c90c48.png)


# 17.Nacos服务实例的权重设置

权重数值越大则访问的次数越多，设置为0时则不会被访问。
![](https://pic1.imgdb.cn/item/69e1dd1aacf11c5279c90c81.png)

# 18.Nacos环境隔离

![](https://pic1.imgdb.cn/item/69e1deefacf11c5279c90f7c.png)
![](https://pic1.imgdb.cn/item/69e1defbacf11c5279c90f96.png)
![](https://pic1.imgdb.cn/item/69e1df06acf11c5279c90faa.png)

重启服务可以看到orderserver在dev环境下。public中就没有了
![](https://pic1.imgdb.cn/item/69e1df15acf11c5279c90fc6.png)
此时访问接口会报错！
![](https://pic1.imgdb.cn/item/69e1df2bacf11c5279c90ffe.png)
![](https://pic1.imgdb.cn/item/69e1df36acf11c5279c9100e.png)
![](https://pic1.imgdb.cn/item/69e1df48acf11c5279c91035.png)
不同环境下的服务是不可见的。



# 19.Nacos和Eureka的对比
![](https://pic1.imgdb.cn/item/69e1df62acf11c5279c91065.png)

## 19.1临时实例和非临时实例

一般默认的实例都为临时实例，非临时实例需要另外配置。

什么是临时实例呢？

临时实例是每30秒主动向Nacos发送心跳监测，如果超过时间没有发送请求，则会被Nacos从服务列表中去除。

什么是非临时实例呢？

非临时实例是Nacos主动访问服务，如果访问没有响应，也不会将它从服务列表删除，只会将服务标记为不健康的状态，并将服务变更的信息主动推送给服务消费者。

![](https://pic1.imgdb.cn/item/69e1df7facf11c5279c91095.png)
![](https://pic1.imgdb.cn/item/69e1df88acf11c5279c910a4.png)
服务注册到Nacos时，可以选择注册为临时或非临时实例，通过下面的配置来设置
![](https://pic1.imgdb.cn/item/69e1df98acf11c5279c910c3.png)
ephemeral: false # 是否是临时实例
![](https://pic1.imgdb.cn/item/69e1dfaaacf11c5279c910e2.png)
总结：

Nacos和Eureka的区别：

1.Nacos支持服务端主动监测提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式。

2.临时实例心跳不正常会被剔除，非临时实例则不会被剔除。

3.Nacos支持服务列表变更的消息推送模式，服务列表更新更及时。Eureka只能支持心跳检测。

4.Nacos集群默认采用AP方式，当集群中群在非临时实例时，采用CP模式；Eureka采用AP方式。

CAP理论：

- **C - 一致性：**
    - **定义：** 所有节点在同一时间看到的数据是完全相同的。就像你去查余额，不管访问哪个银行的服务器，看到的数字都一样。
    - **通俗解释：** 数据写入成功后，任何后续的读取操作都必须返回这个最新的值。如果系统返回了旧数据，就说明一致性被破坏了。
- **A - 可用性：**
    - **定义：** 每次请求都能收到一个（非错误的）响应，但不保证响应中的数据是最新的。
    - **通俗解释：** 系统要保证"一直能用"，不能宕机。哪怕内部数据正在同步，客户端发来请求，系统也得回一句"您好，我在"，而不是直接超时或报错。
- **P - 分区容错性：**
    - **定义：** 系统中任意信息的丢失或节点故障，系统还能继续运行。
    - **通俗解释：** 简单说就是"网络不稳定"或"网线断了"时，系统不能崩溃。这是分布式系统的必选项，因为网络故障是必然会发生的。

20.Nacos配置管理
![](https://pic1.imgdb.cn/item/69e1e02cacf11c5279c911bd.png)
![](https://pic1.imgdb.cn/item/69e1e036acf11c5279c911e3.png)
![](https://pic1.imgdb.cn/item/69e1e042acf11c5279c911fb.png)
![](https://pic1.imgdb.cn/item/69e1e04cacf11c5279c91211.png)
21.Nacos配置拉取
![](https://pic1.imgdb.cn/item/69e1e05dacf11c5279c9123c.png)
项目启动的顺序原本是先读取本地配置文件application.yml。当使用nacos做配置管理时，顺序变为先读取nacos中配置文件，再读取本地配置文件application.yml，两个文件中的配置结合生效。但项目启动时候还没有地方会知道nacos的地址，访问不了nacos。那么就需要在引入优先级更高的配置文件：bootstrap.yml    
![](https://pic1.imgdb.cn/item/69e1e074acf11c5279c91254.png)

```yaml
<!--nacos的配置管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

![](https://pic1.imgdb.cn/item/69e1e097acf11c5279c9127c.png)



```yaml
    #先删除application.yml中相同的配置信息
    spring:
      application:
        name: userservice
      profiles:
        active: dev # 环境
      cloud:
        nacos:
          server-addr: http://10.0.6.52:8848 # nacos地址
          discovery:
            cluster-name: SH  #集群名称 例如福建、上海、广州......
          config:
            file-extension: yaml # 文件后缀名
```

测试是否能取到Nacos上配置的信息
![](https://pic1.imgdb.cn/item/69e1e0b5acf11c5279c91325.png)
![](https://pic1.imgdb.cn/item/69e1e0c5acf11c5279c913cb.png)

# 22.配置热更新

## 方式一（通过注解@RefreshScope      属性刷新）

![](https://pic1.imgdb.cn/item/69e1e38bacf11c5279c921c0.png)

![](https://pic1.imgdb.cn/item/69e1e39aacf11c5279c921d9.png)


```java
//属性刷新
//@RefreshScope   
```

修改参数后，不用重启服务，直接访问可以看到已经发生变化
![](https://pic1.imgdb.cn/item/69e1e3e6acf11c5279c9224c.png)
![](https://pic1.imgdb.cn/item/69e1e3fbacf11c5279c92261.png)
![](https://pic1.imgdb.cn/item/69e1e409acf11c5279c92272.png)

方式二（通过@ConfigurationProperties(prefix = "pattern")注入属性）推荐
![](https://pic1.imgdb.cn/item/69e1e41cacf11c5279c9229a.png)

![](https://pic1.imgdb.cn/item/69e1e42bacf11c5279c922ad.png)

23.多环境配置共享（开发、生成、测试等环境）
![](https://pic1.imgdb.cn/item/69e1e440acf11c5279c922bc.png)
创建配置
![](https://pic1.imgdb.cn/item/69e1e450acf11c5279c922de.png)

24.Feign-基于Feign的远程调用
![](https://pic1.imgdb.cn/item/69e1e46cacf11c5279c9230f.png)

1.引入依赖


```xml
<!--feign客户端依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

![](https://pic1.imgdb.cn/item/69e1e49dacf11c5279c92351.png)

2.启动类加上注解@EnableFeignClients

![](https://pic1.imgdb.cn/item/69e1e4afacf11c5279c9236c.png)
3.在调用方创建Feign接口。@FeignClient(被调用方的方法名)

![](https://pic1.imgdb.cn/item/69e1e4bdacf11c5279c9238c.png)
![](https://pic1.imgdb.cn/item/69e1e4c8acf11c5279c92396.png)
![](https://pic1.imgdb.cn/item/69e1e4d3acf11c5279c923a9.png)
访问接口
![](https://pic1.imgdb.cn/item/69e1e4e3acf11c5279c923ba.png)
feign内置了Ribbon，实现了负载均衡。
![](https://pic1.imgdb.cn/item/69e1e4f2acf11c5279c923e1.png)
访问2次接口可以看到
![](https://pic1.imgdb.cn/item/69e1e504acf11c5279c923f6.png)
![](https://pic1.imgdb.cn/item/69e1e515acf11c5279c92408.png)


# 25.Feign-自定义配置
![](https://pic1.imgdb.cn/item/69e1e731acf11c5279c93185.png)

配置方式1（配置文件方式）、
![](https://pic1.imgdb.cn/item/69e1e748acf11c5279c931ab.png)
访问接口后查看控制台可以看到远程调用的日志
![](https://pic1.imgdb.cn/item/69e1e759acf11c5279c931d9.png)
配置方式2（配置类方式）、
![](https://pic1.imgdb.cn/item/69e1e769acf11c5279c931e7.png)
![](https://pic1.imgdb.cn/item/69e1e773acf11c5279c931f3.png)
![](https://pic1.imgdb.cn/item/69e1e783acf11c5279c93211.png)
访问接口后
![](https://pic1.imgdb.cn/item/69e1e794acf11c5279c93239.png)

26.Feign的性能优化
![](https://pic1.imgdb.cn/item/69e1e7a4acf11c5279c9324d.png)

引入依赖

```xml
<!--引入HttpClient依赖-->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-httpclient</artifactId>
</dependency>
```
![](https://pic1.imgdb.cn/item/69e1e7c2acf11c5279c9327c.png)



```yaml
feign:
  httpclient:
    enabled: true  #支持httpclient开关
    max-connections: 200 #最大连接数   什么值最合适要根据现场用jmeter压测后才知道，微调。
    max-connections-per-route: 50 #单个路径的最大连接数
```

![](https://pic1.imgdb.cn/item/69e1e7e3acf11c5279c932cf.png)

27.Feign的最佳实践

![](https://pic1.imgdb.cn/item/69e1e7f7acf11c5279c932eb.png)
![](https://pic1.imgdb.cn/item/69e1e804acf11c5279c9330a.png)
1.创建module，选择为maven项目

2.导入feign依赖
![](https://pic1.imgdb.cn/item/69e1e815acf11c5279c93327.png)
![](https://pic1.imgdb.cn/item/69e1e821acf11c5279c9333b.png)
3.其他服务引用刚创建的模板feign-api
![](https://pic1.imgdb.cn/item/69e1e835acf11c5279c93375.png)
4.导包报错的地方重新导包

5.此时会报错无法自动装配。因为默认扫描的包是启动类所在的包，所以扫描不到userClient
![](https://pic1.imgdb.cn/item/69e1e848acf11c5279c93394.png)
![](https://pic1.imgdb.cn/item/69e1e859acf11c5279c933a2.png)

6.启动类上的注解@EnableFeignClients中添加属性指定Client所在的包
![](https://pic1.imgdb.cn/item/69e1e86aacf11c5279c933c5.png)



# 28.Gateway网关
![](https://pic1.imgdb.cn/item/69e1e88eacf11c5279c933f7.png)
技术实现有2种：gateway和zuul
![](https://pic1.imgdb.cn/item/69e1e89bacf11c5279c93412.png)

# 29.Gateway网关快速入门

1.创建module，选择为springboot项目

2.引入网关依赖

```xml
<!--网关gateway依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

![](https://pic1.imgdb.cn/item/69e1e8b7acf11c5279c93431.png)

3.网关也是一个微服务项目，也需要注册为服务，所以还需要引入服务发现的依赖

```xml
<!--nacos服务注册发现依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

4.网关配置
![](https://pic1.imgdb.cn/item/69e1e8caacf11c5279c93446.png)
![](https://pic1.imgdb.cn/item/69e1e8d3acf11c5279c93468.png)
请求是先到网关->网关从Nacos中拉取服务列表->通过路由规则访问对应的服务
![](https://pic1.imgdb.cn/item/69e1e8e1acf11c5279c93477.png)

30.路由断言工厂
![](https://pic1.imgdb.cn/item/69e1eac0acf11c5279c937cd.png)
常用的是Path。配置多个断言时需要都满足才可以通过校验。
![](https://pic1.imgdb.cn/item/69e1ead0acf11c5279c937ef.png)
![](https://pic1.imgdb.cn/item/69e1eae1acf11c5279c93816.png)
否则会报错404
![](https://pic1.imgdb.cn/item/69e1eaf0acf11c5279c93829.png)



# 31.GatewayFilter过滤器
![](https://pic1.imgdb.cn/item/69e1eb05acf11c5279c93854.png)
![](https://pic1.imgdb.cn/item/69e1eb2facf11c5279c938af.png)
![](https://pic1.imgdb.cn/item/69e1eb3bacf11c5279c938c9.png)
测试（访问user服务才行，如果是通过order访问user是为空的！）
![](https://pic1.imgdb.cn/item/69e1eb4bacf11c5279c938f9.png)
通过网关访问order服务，order服务内部调用user服务时**为空**
![](https://pic1.imgdb.cn/item/69e1eb58acf11c5279c93908.png)
![](https://pic1.imgdb.cn/item/69e1ebb5acf11c5279c939ce.png)
通过网关直接访问到user服务时才能获取到
![](https://pic1.imgdb.cn/item/69e1ebc7acf11c5279c93a01.png)
![](https://pic1.imgdb.cn/item/69e1ebd4acf11c5279c93a14.png)

# 32.默认过滤器defaultFilter

defalultFilter过滤器和gatewayFilter配置只在位置上有区别，但却是2个不同的过滤器。gatewayFilter在每个服务下都写一遍，defalultFilter只需在default-filters下写即可。
![](https://pic1.imgdb.cn/item/69e1ebecacf11c5279c93a40.png)

# 33.全局过滤器GlobalFilter

GatewayFilter是通过配置定义，处理逻辑是固定的。而GlobalFilter的逻辑需要自己写代码实现。
![](https://pic1.imgdb.cn/item/69e1ec0cacf11c5279c93a91.png)
![](https://pic1.imgdb.cn/item/69e1ec16acf11c5279c93a9a.png)
访问接口报错401
![](https://pic1.imgdb.cn/item/69e1ec22acf11c5279c93ab4.png)
参数中带上?authorization=admin
![](https://pic1.imgdb.cn/item/69e1ec30acf11c5279c93ad5.png)
拦截器可以写多个，那如何指定拦截器的顺序呢？
![](https://pic1.imgdb.cn/item/69e1ec3facf11c5279c93aed.png)
![](https://pic1.imgdb.cn/item/69e1ec4bacf11c5279c93af7.png)
当3个过滤器的order有相等的时候，会按照defaultFilter>gatewayFilter>GlobalFilter

# 34.Docker是什么？

​     [02-初识Docker-什么是docker_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1LQ4y127n4/?p=43&spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=12cb2e54cff5d0b62b7e96226f2c7d73)

![](https://pic1.imgdb.cn/item/69e1ec92acf11c5279c93bb8.png)

![](https://pic1.imgdb.cn/item/69e1ec9dacf11c5279c93be6.png)

![](https://pic1.imgdb.cn/item/69e1ecabacf11c5279c93bf8.png)

35.Docker与虚拟机

![](https://pic1.imgdb.cn/item/69e1ecc3acf11c5279c93c23.png)

36.Docker架构
![](https://pic1.imgdb.cn/item/69e1ecd8acf11c5279c93c69.png)
![](https://pic1.imgdb.cn/item/69e1ece2acf11c5279c93c7c.png)


# 37.安装Docker

## 卸载（可选）

如果之前安装过旧版本的Docker，可以使用下面命令卸载：

```sh
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine \
                  docker-ce
```

连接服务器执行命令，安装yum工具

```sh
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2 --skip-broken
```

![](https://pic1.imgdb.cn/item/69e1eeb6acf11c5279c94840.png)

更新本地镜像源

```sh
# 设置docker镜像源
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo

yum makecache fast
```

安装CE版本的Docker

```sh
yum install -y docker-ce
```



启动docker

Docker应用需要用到各种端口，逐一去修改防火墙设置。这边直接关闭防火墙！

本地启动docker前，一定要关闭防火墙后！！

```sh
# 关闭
systemctl stop firewalld
# 禁止开机启动防火墙
systemctl disable firewalld
```

云服务器则通过设置开放的端口。

通过命令启动docker

```sh
systemctl start docker  # 启动docker服务、

systemctl status docker  # 查看docker服务状态

systemctl stop docker  # 停止docker服务

systemctl restart docker  # 重启docker服务

docker -v #查看docker版本
```

执行systemctl status docker查看docker状态

![](https://pic1.imgdb.cn/item/69e1eecbacf11c5279c9498c.png)

配置镜像加速

docker官方镜像仓库网速较差，我们需要设置国内镜像服务：

参考阿里云的镜像加速文档：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
    "https://2a6bf1988cb6428c877f723ec7530dbc.mirror.swr.myhuaweicloud.com",
    "https://docker.m.daocloud.io",
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com",
    "https://your_preferred_mirror",
    "https://dockerhub.icu",
    "https://docker.registry.cyou",
    "https://docker-cf.registry.cyou",
    "https://dockercf.jsdelivr.fyi",
    "https://docker.jsdelivr.fyi",
    "https://dockertest.jsdelivr.fyi",
    "https://mirror.aliyuncs.com",
    "https://dockerproxy.com",
    "https://mirror.baidubce.com",
    "https://docker.m.daocloud.io",
    "https://docker.nju.edu.cn",
    "https://docker.mirrors.sjtug.sjtu.edu.cn",
    "https://docker.mirrors.ustc.edu.cn",
    "https://mirror.iscas.ac.cn",
    "https://docker.rainbond.cc"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```





# 38.镜像基本操作
![](https://pic1.imgdb.cn/item/69e1eee0acf11c5279c94a6e.png)
镜像操作命令
![](https://pic1.imgdb.cn/item/69e1eeedacf11c5279c94b10.png)
dockerhub官网地址[Docker Official Images](https://hub.dockermirror.com/u/library)

拉取Nginx镜像

```shell
docker pull nginx
```

查看镜像列表

```
docker images
```
![](https://pic1.imgdb.cn/item/69e1ef18acf11c5279c94d5b.png)
导出镜像到磁盘

```shell
docker save
例如：docker save -o nginx.tar nginx:latest
docker save -o是固定的
nginx.tar  是导出后的文件名称
nginx:latest 是指要导出的镜像名称及版本
```
![](https://pic1.imgdb.cn/item/69e1ef45acf11c5279c94faa.png)
通过命令ll查看文件列表
![](https://pic1.imgdb.cn/item/69e1ef53acf11c5279c95094.png)
删除镜像（不是删除刚刚导出的文件）
![](https://pic1.imgdb.cn/item/69e1ef62acf11c5279c95133.png)
导入刚刚导出的镜像文件
![](https://pic1.imgdb.cn/item/69e1ef72acf11c5279c95222.png)

# 39.容器基本操作

镜像通过docker run命令可以创建容器、并且运行容器
![](https://pic1.imgdb.cn/item/69e1efc2acf11c5279c955cf.png)

创建并且运行Nginx容器：
![](https://pic1.imgdb.cn/item/69e1efd5acf11c5279c9569a.png)

根据镜像创建容器并运行容器

```docker
docker run --name mn -d -p 80:80 nginx  
```

查看所有容器

```
docker ps
```

![](https://pic1.imgdb.cn/item/69e1efeaacf11c5279c9579d.png)

访问服务器的80端口可以看到Welcome to nginx!

![](https://pic1.imgdb.cn/item/69e1eff8acf11c5279c95850.png)

查看Nginx日志

```
docker logs 容器名称 
例如：docker logs mn
```
![](https://pic1.imgdb.cn/item/69e1f01bacf11c5279c95910.png)

容器内文件修改
![](https://pic1.imgdb.cn/item/69e1f029acf11c5279c95922.png)

进入容器

```
docker exec -it mn bash
```

![](https://pic1.imgdb.cn/item/69e1f03eacf11c5279c95939.png)

ls查看当前目录下所有文件

![](https://pic1.imgdb.cn/item/69e1f04eacf11c5279c95964.png)

进入Nginx静态文件

```
cd usr/share/nginx/html 
```

![](https://pic1.imgdb.cn/item/69e1f063acf11c5279c9597e.png)

通过vi编辑时候会报错命令找不到，这是因为没有安装编辑器。

![](https://pic1.imgdb.cn/item/69e1f074acf11c5279c9599f.png)

可通过以下直接修改

```sh
#找到Welcome to nginx修改为我是测试测试的
sed -i 's#Welcome to nginx#我是测试测试的#g' index.html   
sed -i 's#<head>#<head><meta charset="utf-8">#g' index.html
```

![](https://pic1.imgdb.cn/item/69e1f08dacf11c5279c959d6.png)
退出当前容器输入exit
![](https://pic1.imgdb.cn/item/69e1f09bacf11c5279c959e6.png)

停止docker容器

```docker
docker stop  容器名称
#例如：docker stop mn
```
![](https://pic1.imgdb.cn/item/69e1f0b3acf11c5279c95a05.png)

此时通过docker ps是查不出容器的，docker ps默认是查询运行的容器。如果停止的容器也想查询

```docker
#查询所有的容器（包括停止的）
docker ps -a
```
![](https://pic1.imgdb.cn/item/69e1f0cfacf11c5279c95a31.png)

停止状态启动容器

```docker
docker stop 容器名称
#例如：docker stop mn
```



删除容器

```docker
docker rm 容器名称
#例如：docker rm mn  (先停止容器才能删除)
#例如：docker rm -f mn  (强制删除容器，即使运行状态也能删除)
```



创建并运行Redis容器

```docker
docker run --name myredis -d -p 6379:6379 redis redis-server --appendonly yes

#redis-server - 启动 Redis 服务器

#--appendonly yes - 开启 AOF（Append Only File）持久化模式
```



进入Redis容器

```sh
docker exec -it myredis bash   #进入redis容器
redis-cli   #进入redis客户端
keys *   #查看所有键
set num 666  #增加键值对
set num   #获取num值
```

![](https://pic1.imgdb.cn/item/69e1f0f7acf11c5279c95a6a.png)

# 40.数据卷

[12-使用Docker-数据卷命令_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1LQ4y127n4?spm_id_from=333.788.player.switch&vd_source=12cb2e54cff5d0b62b7e96226f2c7d73&p=53)

https://pic1.imgdb.cn/item/69e1f22facf11c5279c95c49.png

https://pic1.imgdb.cn/item/69e1f23bacf11c5279c95c56.png

创建数据卷

```docker
docker volume create 数据卷名称
#例如 docker volume create html
```

查看所有数据卷

```docker
docker volume ls
```

查看数据卷的详细信息

```docker
docker volume inspect 数据卷名称
#例如docker volume inspect html
```

https://pic1.imgdb.cn/item/69e1f25eacf11c5279c95c84.png

删除数据卷

```docker
docker volume prune   #删除未使用的数据卷
docker volume rm 数据卷名称  #删除对应的数据卷   例如：docker volume rm html
```

# 41.数据卷挂载

之前都是进入到容器中改内容，挂载数据卷之后数据和容器就分开了。

https://pic1.imgdb.cn/item/69e1f27dacf11c5279c95cb7.png

将naginx的html文件/usr/share/nginx/html，把这个目录挂载到html这个数据卷上，方便操作其中的内容。

如果数据卷html不存在，则会自动创建，可以不需要手动再去另外创建数据卷。

```docker
docker run --name mynginx -d -p 80:80 -v html:/usr/share/nginx/html nginx
```

https://pic1.imgdb.cn/item/69e1f2b0acf11c5279c95cfb.png

先通过docker volume inspect html查出数据卷的位置，cd进入数据卷。查看是否挂载成功

https://pic1.imgdb.cn/item/69e1f2c2acf11c5279c95d12.png

https://pic1.imgdb.cn/item/69e1f2d4acf11c5279c95d2c.png

# 42.目录挂载

这是直接挂载到宿主机自定义的目录，数据卷挂载是创建数据卷时候会自动生成文件的位置，位置是docker生成的。

以MySql为例

```docker
docker pull mysql
```

tmp目录下创建文件夹，分别用来存储mysql的配置文件和mysql的数据

https://pic1.imgdb.cn/item/69e1f2f9acf11c5279c95d5b.png

conf文件夹中放入文件

[hmy.cnf](D:\BaiduNetdiskDownload\hmy.cnf) 


```docker
docker run --name mymysql \ #因为一行比较模糊，这边用\换行符号，代码命令还没结束
-e MYSQL_ROOT_PASSWORD=123456 -d -p 3306:3306 \
-v /tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf \     
#/tmp/mysql/conf/hmy.cnf宿主机文件   /etc/mysql/conf.d/hmy.cnf容器内文件
-v /tmp/mysql/data:/var/lib/mysql \								  
#/tmp/mysql/data宿主机目录  /var/lib/mysq容器内目录
mysql:latest

直接复制一下
docker run --name mymysql \
-e MYSQL_ROOT_PASSWORD=123456 -d -p 3306:3306 \
-v /tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf \
-v /tmp/mysql/data:/var/lib/mysql \
mysql:latest
```

启动后但是容器状态是停止的状态。通过docker logs mymysql可以查看到具体报错日志

（MySQL 9.6 版本不支持配置文件中直接使用 `utf8`，需要使用 `utf8mb4` 或 `utf8mb3`。需修改hmy.cnf文件中的配置）

https://pic1.imgdb.cn/item/69e1f32facf11c5279c95dab.png

https://pic1.imgdb.cn/item/69e1f339acf11c5279c95dba.png

42.自定义镜像

https://pic1.imgdb.cn/item/69e1f359acf11c5279c95deb.png
什么是DockerFile

https://pic1.imgdb.cn/item/69e1f367acf11c5279c95dfd.png
https://pic1.imgdb.cn/item/69e1f370acf11c5279c95e05.png
https://pic1.imgdb.cn/item/69e1f37cacf11c5279c95e20.png

运行命令

```docker
docker build -t javaweb:1.0 .
#docker build -t 名称:版本 dockerfile的所在目录(.代表当前目录)
```

https://pic1.imgdb.cn/item/69e1f392acf11c5279c95e58.png

运行容器

```docker
docker run --name web -d -p 8090:8090 javaweb:1.0
```
https://pic1.imgdb.cn/item/69e1f3aaacf11c5279c95e85.png

访问接口
https://pic1.imgdb.cn/item/69e1f3b8acf11c5279c95e9b.png
dockerfile文件内优化
https://pic1.imgdb.cn/item/69e1f3c9acf11c5279c95ea9.png

已经有镜像打包好了这些，所以可以缩写为

```docker
FROM azul/zulu-openjdk-alpine:8
```
https://pic1.imgdb.cn/item/69e1f3e3acf11c5279c95ed9.png

重新构建

```docker
docker build -t javaweb2.0 .
```


# 43.DockerCompose

[17-DockerCompose-初始Compose_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1LQ4y127n4/?p=58&spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=12cb2e54cff5d0b62b7e96226f2c7d73)

https://pic1.imgdb.cn/item/69e1f462acf11c5279c95f78.png

之前使用docker run命令运行容器，Compose可以理解为docker run命令的集合。但不是直接用docker run,而是用了指令来代替。

将上面的配置对比我们之前的docker run命令

```
docker run --name mymysql \
-e MYSQL_ROOT_PASSWORD=123456 -d -p 3306:3306 \
-v /tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf \
-v /tmp/mysql/data:/var/lib/mysql \
mysql:latest
```



安装DockerCompose（最新版docker已经自带）

```docker
docker compose version
```

https://pic1.imgdb.cn/item/69e1f48aacf11c5279c95fb4.png

安装Nacos

```docker
docker pull nacos/nacos-server
```

创建容器

```
docker run -d \
>   --name nacos-server \
>   -p 8848:8848 \
>   -p 8080:8080 \
>   -p 9848:9848 \
>   -p 9849:9849 \
>   -e MODE=standalone \
>   -e NACOS_AUTH_ENABLE=true \
>   -e NACOS_AUTH_TOKEN="VGhpcyBpcyBhIDMyIGJ5dGVzIGxvbmcgc2VjcmV0IGtleSBmb3IgbmFjb3M=" \
>   -e NACOS_AUTH_IDENTITY_KEY="nacos" \
>   -e NACOS_AUTH_IDENTITY_VALUE="nacos" \
>   nacos/nacos-server:latest
```

44.同步通讯的优缺点
https://pic1.imgdb.cn/item/69e1f4afacf11c5279c95ff9.png

https://pic1.imgdb.cn/item/69e1f4b9acf11c5279c9600b.png

https://pic1.imgdb.cn/item/69e1f4d2acf11c5279c96030.png

# 45.异步通讯的优缺点

[03-初识MQ--异步通讯的优缺点_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1LQ4y127n4?spm_id_from=333.788.videopod.episodes&vd_source=12cb2e54cff5d0b62b7e96226f2c7d73&p=63)

https://pic1.imgdb.cn/item/69e1f4ddacf11c5279c96044.png

https://pic1.imgdb.cn/item/69e1f4e5acf11c5279c9606b.png

46.初识MQ
https://pic1.imgdb.cn/item/69e1f4f3acf11c5279c960ad.png

# 47.安装RabbitMQ

拉取镜像

```docker
docker pull rabbitmq:management
```

运行

```
docker run -d \           
--name mq \               # 给容器命名为 "mq"
--hostname mq1 \          # 设置容器的主机名为 "mq1"
-p 15672:15672 \          # 端口映射：主机15672 -> 容器15672（RabbitMQ管理界面端口）
-p 5672:5672 \            # 端口映射：主机5672 -> 容器5672（RabbitMQ服务端口）
rabbitmq:management       # 使用的镜像名称（带管理插件的 RabbitMQ 版本）

直接运行下面指令
docker run -d --name mq --hostname mq1 -p 15672:15672 -p 5672:5672 rabbitmq:management
```



