---
title: redis
date: 2024-01-19 00:11:31
comments: true #是否可评论
tags: # 文章的标签
	- Redis
	- 非关系型数据库
categories: 
	- 数据库
description: redis的学习	  
top_img: https://pic.imgdb.cn/item/65a94ffc871b83018af68648.jpg     #顶部图片
cover: https://pic.imgdb.cn/item/65a94ffc871b83018af68648.jpg    #主页中显示的图片
---



# 一、什么是redis

​        首先要说redis,应该先说一下nosql,NoSQL(NoSQL = Not Only SQL )，意即“不仅仅是SQL”，泛指非关系型的数据库。随着互联网web2.0网站的兴起，传统的关系数据库在应付web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题，包括超大规模数据的存储。（例如谷歌或Facebook每天为他们的用户收集万亿比特的数据）。这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。
​        Redis:REmote DIctionary Server(远程字典服务器)是完全开源免费的，用C语言编写的，遵守BSD协议，是一个高性能的(key/value)分布式内存数据库，基于内存运行并支持持久化的NoSQL数据库，是当前最热门的NoSql数据库之一,也被人们称为数据结构服务器。

# 二、redis使用场景

## 1、热点数据的缓存

​        由于redis访问速度块、支持的数据类型比较丰富，所以redis很适合用来存储热点数据，另外结合expire，我们可以设置过期时间然后再进行缓存更新操作，这个功能最为常见，我们几乎所有的项目都有所运用。

## 2、限时业务的运用

​        redis中可以使用expire命令设置一个键的生存时间，到时间后redis会删除它。利用这一特性可以运用在限时的优惠活动信息、手机验证码等业务场景。

## 3、计数器相关问题

​        redis由于incrby命令可以实现原子性的递增，所以可以运用于高并发的秒杀活动、分布式序列号的生成、具体业务还体现在比如限制一个手机号发多少条短信、一个接口一分钟限制多少请求、一个接口一天限制调用多少次等等。

## 4、排行榜相关问题

​        关系型数据库在排行榜方面查询速度普遍偏慢，所以可以借助redis的SortedSet进行热点数据的排序。

​        在奶茶活动中，我们需要展示各个部门的点赞排行榜， 所以我针对每个部门做了一个SortedSet,然后以用户的openid作为上面的username,以用户的点赞数作为上面的score, 然后针对每个用户做一个hash,通过zrangebyscore就可以按照点赞数获取排行榜，然后再根据username获取用户的hash信息，这个当时在实际运用中性能体验也蛮不错的。

## 5、分布式锁

​        这个主要利用redis的setnx命令进行，setnx："set if not exists"就是如果不存在则成功设置缓存同时返回1，否则返回0 ，这个特性在俞你奔远方的后台中有所运用，因为我们服务器是集群的，定时任务可能在两台机器上都会运行，所以在定时任务中首先 通过setnx设置一个lock，如果成功设置则执行，如果没有成功设置，则表明该定时任务已执行。 当然结合具体业务，我们可以给这个lock加一个过期时间，比如说30分钟执行一次的定时任务，那么这个过期时间设置为小于30分钟的一个时间 就可以，这个与定时任务的周期以及定时任务执行消耗时间相关。

​        当然我们可以将这个特性运用于其他需要分布式锁的场景中，结合过期时间主要是防止死锁的出现。

## 6、延时操作

​        这个目前我做过相关测试，但是还没有运用到我们的实际项目中，下面我举个该特性的应用场景。 比如在订单生产后我们占用了库存，10分钟后去检验用户是够真正购买，如果没有购买将该单据设置无效，同时还原库存。 由于redis自2.8.0之后版本提供Keyspace Notifications功能，允许客户订阅Pub/Sub频道，以便以某种方式接收影响Redis数据集的事件。 所以我们对于上面的需求就可以用以下解决方案，我们在订单生产时，设置一个key，同时设置10分钟后过期， 我们在后台实现一个监听器，监听key的实效，监听到key失效时将后续逻辑加上。 当然我们也可以利用rabbitmq、activemq等消息中间件的延迟队列服务实现该需求。

## 7、分页、模糊搜索

​        redis的set集合中提供了一个zrangebylex方法，语法如下：

```
ZRANGEBYLEX key min max [LIMIT offset count]
```

​       通过ZRANGEBYLEX zset - + LIMIT 0 10 可以进行分页数据查询，其中- +表示获取全部数据

zrangebylex key min max 这个就可以返回字典区间的数据，利用这个特性可以进行模糊查询功能，这个也是目前我在redis中发现的唯一一个支持对存储内容进行模糊查询的特性。

​        前几天我通过这个特性，对学校数据进行了模拟测试，学校数据60万左右，响应时间在700ms左右，比mysql的like查询稍微快一点，但是由于它可以避免大量的数据库io操作，所以总体还是比直接mysql查询更利于系统的性能保障。

## 8、点赞、好友等相互关系的存储

​        Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。 又或者在微博应用中，每个用户关注的人存在一个集合中，就很容易实现求两个人的共同好友功能。

​        这个在奶茶活动中有运用，就是利用set存储用户之间的点赞关联的，另外在点赞前判断是否点赞过就利用了sismember方法，当时这个接口的响应时间控制在10毫秒内，十分高效。

## 9、队列

​        由于redis有list push和list pop这样的命令，所以能够很方便的执行队列操作。

# 三、SpringBoot使用redis步骤

## 1、添加依赖

```xml
        <!-- redis的启动器依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

## 2、在application.yml里进行连接

```yaml
spring:
  redis:
    port: 6379            # 服务器端口
    host: localhost       # r服务器地址
    database: 0           # 数据库索引（默认0）
    jedis:
      pool:
        max-active: 50      # 连接池最大连接数（使用负值表示没有限制）
        max-wait: 3000ms    # 连接池最大阻塞等待时间（使用负值表示没有限制）
        max-idle: 20        # 连接池中的最大空闲连接数
        min-idle: 2         # 连接池中的最小空闲连接数
    connect-timeout: 5000ms # 连接超时时间（毫秒）

```

## 3、工具类RedisUtil

```java
package com.chengfurong.utils;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;

import java.util.concurrent.TimeUnit;



/**
 * @ClassName RedisUtil
 * @Description TODO
 * @Author chengfurong
 * @Date 2021/05/24 21:36
 * @Version 1.0
 **/
@Component
public class RedisUtil {
    @Autowired
    private RedisTemplate<String,String> template;

    /**
     * 读取数据
     *
     * @param key
     * @return
     */
    public String get(final  String key){
        return template.opsForValue().get(key);
    }
    /**
     * 写入数据
     */
    public boolean set(final String key, String value) {
        boolean res = false;
        try {
            template.opsForValue().set(key, value);
            res = true;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return res;
    }

    /**
     * 根据key更新数据
     */
    public boolean update(final String key, String value) {
        boolean res = false;
        try {
            template.opsForValue().getAndSet(key, value);
            res = true;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return res;
    }

    /**
     * 根据key删除数据
     */
    public boolean del(final String key) {
        boolean res = false;
        try {
            template.delete(key);
            res = true;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return res;
    }

    /**
     * 是否存在key
     */
    public boolean hasKey(final String key) {
        boolean res = false;
        try {
            res = template.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return res;
    }

    /**
     * 给指定的key设置存活时间
     * 默认为-1，表示永久不失效
     */
    public boolean setExpire(final String key, long seconds) {
        boolean res = false;
        try {
            if (0 < seconds) {
                res = template.expire(key, seconds, TimeUnit.SECONDS);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return res;
    }

    /**
     * 获取指定key的剩余存活时间
     * 默认为-1，表示永久不失效，-2表示该key不存在
     */
    public long getExpire(final String key) {
        long res = 0;
        try {
            res = template.getExpire(key, TimeUnit.SECONDS);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return res;
    }

    /**
     * 移除指定key的有效时间
     * 当key的有效时间为-1即永久不失效和当key不存在时返回false，否则返回true
     */
    public boolean persist(final String key) {
        boolean res = false;
        try {
            res = template.persist(key);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return res;
    }
}

```

## 4、测试类

```java
package com.chengfurong;

import com.chengfurong.utils.RedisUtil;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.util.Assert;

import java.util.List;

@SpringBootTest
class ForthshoppingbootApplicationTests {

    @Autowired
    private RedisUtil redisUtil;

    @Test
    void redisTest(){
        boolean re = redisUtil.set("key1","haha");
        Assert.isTrue(re,"success");
    }

}

```

# 四、Redis和MySQL如何保持数据一致性?
        在高并发的场景下，大量的请求直接访问Mysql很容易造成性能问题。所以，我们都会用Redis来做数据的缓存，削减对数据库的请求。但是，Mysql和Redis是两种不同的数据库，如何保证不同数据库之间数据的一致性就非常关键了。


## 1、导致数据不一致的原因

- 1、在高并发的业务场景下，数据库大多数情况都是用户并发访问最薄弱的环节。
- 2、所以，就需要使用redis做一个缓冲操作，让请求先访问到redis，而不是直接访问MySQL等数据库。
- 3、读取缓存步骤一般没有什么问题，但是一旦涉及到数据更新：数据库和缓存更新，就容易出现缓存(Redis)和数据库（MySQL）间的数据一致性问题。
- 4、这个业务场景，主要是解决读数据从Redis缓存，一般都是按照下图的流程来进行业务操作。

![](https://pic.imgdb.cn/item/65a94e87871b83018af07806.jpg)
### 1.1、缓存先后删除问题

不管是先写MySQL数据库，再删除Redis缓存；还是先删除缓存，再写库，都有可能出现数据不一致的情况。

### 1.2、先删除缓存

1、如果先删除Redis缓存数据，然而还没有来得及写入MySQL，另一个线程就来读取。
2、这个时候发现缓存为空，则去Mysql数据库中读取旧数据写入缓存，此时缓存中为脏数据。
3、然后数据库更新后发现Redis和Mysql出现了数据不一致的问题。

### 1.3、后删除缓存

1、如果先写了库，然后再删除缓存，不幸的写库的线程挂了，导致了缓存没有删除
2、这个时候就会直接读取旧缓存，最终也导致了数据不一致情况
3、因为写和读是并发的，没法保证顺序,就会出现缓存和数据库的数据不一致的问题

## 2、解决方案

### 2.1、延时双删策略

#### 2.1.1、基本思路

在写库前后都进行redis.del(key)操作，并且设定合理的超时时间。

伪代码如下：

```java
public void write( String key, Object data ){ 
     redis.delKey( key ); 
     db.updateData( data ); 
     Thread.sleep( 500 ); 
     redis.delKey( key );
}
```

#### 2.1.2、具体步骤

1、先删除缓存
2、再写数据库
3、休眠xxx毫秒（根据具体的业务时间来定）
4、再次删除缓存

问题：这个500毫秒怎么确定的，具体该休眠多久时间呢？

1、需要评估自己的项目的读数据业务逻辑的耗时。
2、这么做的目的，就是确保读请求结束，写请求可以删除读请求造成的缓存脏数据。
3、当然这种策略还要考虑redis和数据库主从同步的耗时。
4、最后的的写数据的休眠时间：则在读数据业务逻辑的耗时基础上，加几百ms即可。

比如：休眠1秒。

#### 2.1.3、设置缓存过期时间是关键点

1、从理论上来说，给缓存设置过期时间，是保证最终一致性的解决方案
2、所有的写操作以数据库为准，只要到达缓存过期时间，缓存删除
3、如果后面还有读请求的话，就会从数据库中读取新值然后回填缓存

#### 2.1.4、方案缺点

结合双删策略+缓存超时设置，这样最差的情况就是：

1、在缓存过期时间内发生数据存在不一致
2、同时又增加了写请求的耗时。

#### 2.1.5、问题

​        为什么要双删呢！

​        因为第一次删除的是还没更新前的数据，第二次删除则是因为读取的并发性导致的缓存重新写入数据出现的垃圾数据。
​        如果你们的删缓存失败了，怎么办？那不是还是会出现缓存和数据库不一致的情况么？比如一个写数据请求，然后写入数据库了，删缓存失败了，这会就出现不一致的情况了。

​        这时候我们就需要一个中间件的无私配合了，那就是使用消息来进行重试机制。
步骤：
1、业务代码去更新数据库
2、数据库的操作进行记录日志。
3、订阅程序提取出所需要的数据以及key
4、获得该信息尝试删除缓存，发现删除失败的时候，发送消息到消息队列
5、继续重试删除缓存的操作，直到删除缓存成功。

​        其实这个方法与分布式事务的处理方式，就是保证数据的最终一致性，而在分布式事务中，则称之为这种为最大努力通知。那么最大努力通知又是什么样的流程呢？

1、业务方把通知发送给 MQ
2、接收通知方监听 MQ
3、接收通知方接收消息，业务处理完成回应ack
4、接收通知方若没有回应ack则MQ会重复通知，MQ 按照间隔时间从短到长的方式逐步拉大通知间隔，直到达到通知要求的时间上限，比如24小时之后不再进行通知。
5、接收通知方可通过消息校对接口来校对消息的一致性

**总结：**
         而为什么叫最大努力通知呢，实际上也很容易理解，他并没有从本质上解决问题，只是把问题数目从100 变成了 10 ，毕竟有些内容第一次没处理，第二次就可能会被处理掉。也就是说降低了这种有问题情况的发生，毕竟保证的都是最终一致性。
		 
		 
		 
### 2.2、异步更新缓存（基于Mysql binlog的同步机制）

#### 2.2.1、整体思路

1、涉及到更新的数据操作，利用Mysql binlog 进行增量订阅消费
2、将消息发送到消息队列
3、通过消息队列消费将增量数据更新到Redis上
4、操作情况

读取Redis缓存：热数据都在Redis上
写Mysql：增删改都是在Mysql进行操作
更新Redis数据：Mysql的数据操作都记录到binlog，通过消息队列及时更新到Redis上

#### 2.2.2、Redis更新过程

数据操作主要分为两种：

1、一种是全量（将所有数据一次性写入Redis）
2、一种是增量（实时更新）

这里说的是增量,指的是mysql的update、insert、delate变更数据。

读取binlog后分析 ，利用消息队列,推送更新各台的redis缓存数据。
1、这样一旦MySQL中产生了新的写入、更新、删除等操作，就可以把binlog相关的消息推送至Redis
2、Redis再根据binlog中的记录，对Redis进行更新
3、其实这种机制，很类似MySQL的主从备份机制，因为MySQL的主备也是通过binlog来实现的数据一致性

这里的消息推送工具你也可以采用别的第三方：kafka、rabbitMQ等来实现推送更新Redis!

## 3、总结

​        在高并发应用场景下，如果是对数据一致性要求高的情况下，要定位好导致数据和缓存不一致的原因。

解决高并发场景下数据一致性的方案有两种，分别是延时双删策略和异步更新缓存两种方案。

也可以采用别的第三方：kafka、rabbitMQ等来实现推送更新Redis!
		 
		 
# 五、Redis中雪崩、击穿、穿透

Redis应用中故障及应用：缓存雪崩、缓存穿透、缓存击穿、缓存预热、缓存更新、缓存降级

## 1：如何理解Redis缓存的雪崩、穿透、击穿问题：

### 雪崩：

定义：redis缓存中大量的key同时失效，此时又刚好有大量的请求打进来，直接打到数据库层，造成数据库阻塞甚至宕机。

解决办法：缓存雪崩的事前事中事后的解决方案如下：

- 事前：Redis 高可用，主从+哨兵，Redis cluster，避免全盘崩溃。
  - 原有的失效时间基础上增加一个随机值，比如1-5分钟随机
  - 加锁，当出现缓存失效的情况通过锁控制一定时间只有一个线程获取到资源从数据库拿到数据后写回缓存，后面基于缓存做查询
  - 缓存预热
  - 热点key设置永不过期。
- 事中：本地 ehcache 缓存 + hystrix 限流&降级，避免 MySQL 被打死。
- 事后：Redis 持久化，一旦重启，自动从磁盘上加载数据，快速恢复缓存数据。	 
![](https://pic.imgdb.cn/item/65a94f23871b83018af2f03e.jpg)

### 穿透：

定义：指查询一个缓存和数据库都不存在的数据，导致尽管数据不存在但是每次都会到数据库查询。在访问量大时可能DB就会挂掉。如果有人利用不存在的key频繁攻击，则这就形成了漏洞。

解决办法：

1. 如果一个查询返回的数据为空，我们仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。
2. 接口层增加校验，用户鉴权，id做基础校验。
3. 采用布隆过滤器，将所有可能存在的数据hash到一个足够大的bitmap中。（会有一定的出错率）

布隆过滤器：类似于HashSet，可以快速判断一个元素在集合中是否存在，应用场景：快速判断一个元素是否在某容器内，不存在直接返回。（关键点在于hash算法和容器大小。）

**核心思想：**

- 通过hash函数将一个元素映射到位阵列（bit array）中的一个点
- 多个hash函数，增加随机性，减少hash碰撞的概率。
- 扩大数组范围，使hash值均匀分布，减少hash碰撞的概率。

### 击穿：

击穿与雪崩的不同在于缓存key失效的量级上。击穿是对于单个key值的缓存失效过期，雪崩则是大面积key同时失效。

解决办法：

1. 若缓存数据基本不会发生更新，则可尝试将热点数据设置为永不过期。
2. 若缓存的数据更新不频繁，且缓存刷新的整个流程耗时较少的情况下，则可以采用基于 Redis、zookeeper 等分布式中间件的分布式互斥锁，或者本地互斥锁以保证仅少量的请求能请求数据库并重新构建缓存，其余线程则在锁释放后能访问到新缓存。
3. 若缓存的数据更新频繁或者在缓存刷新的流程耗时较长的情况下，可以利用定时线程在缓存过期前主动地重新构建缓存或者延后缓存的过期时间，以保证所有的请求能一直访问到对应的缓存。




# 六、缓存穿透 + 缓存雪崩 + 缓存击穿的原因和解决方案

​        在生产环境中，会因为很多的原因造成访问请求绕过了缓存，都需要访问数据库持久层，虽然对Redsi缓存服务器不会造成影响，但是数据库的负载就会增大，使缓存的作用降低

## 1、缓存穿透

​       缓存穿透是指查询一个根本不存在的数据，缓存层和持久层都不会命中。在日常工作中出于容错的考虑，如果从持久层查不到数据则不写入缓存层，缓存穿透将导致不存在的数据每次请求都要到持久层去查询，失去了缓存保护后端持久的意义

缓存穿透示意图：
![](https://pic.imgdb.cn/item/65a94f55871b83018af3bc74.jpg)


缓存穿透问题可能会使后端存储负载加大，由于很多后端持久层不具备高并发性，甚至可能造成后端存储宕机。通常可以在程序中统计总调用数、缓存层命中数、如果同一个Key的缓存命中率很低，可能就是出现了缓存穿透问题。

​        造成缓存穿透的基本原因有两个。第一，自身业务代码或者数据出现问题（例如：set 和 get 的key不一致），第二，一些恶意攻击、爬虫等造成大量空命中（爬取线上商城商品数据，超大循环递增商品的ID）

## 2、**解决方案：**

### 2.**1. 缓存空对象**

​        缓存空对象：是指在持久层没有命中的情况下，对key进行set （key,null）

​        缓存空对象会有两个问题：

​        第一，value为null 不代表不占用内存空间，空值做了缓存，意味着缓存层中存了更多的键，需要更多的内存空间，比较有效的方法是针对这类数据设置一个较短的过期时间，让其自动剔除。

​       第二，缓存层和存储层的数据会有一段时间窗口的不一致，可能会对业务有一定影响。例如过期时间设置为5分钟，如果此时存储层添加了这个数据，那此段时间就会出现缓存层和存储层数据的不一致，此时可以利用消息系统或者其他方式清除掉缓存层中的空对象

### **2.2. 布隆过滤器拦截**

​        在访问缓存层和存储层之前，将存在的key用布隆过滤器提前保存起来，做第一层拦截，当收到一个对key请求时先用布隆过滤器验证是key否存在，如果存在在进入缓存层、存储层。可以使用bitmap做布隆过滤器。这种方法适用于数据命中不高、数据相对固定、实时性低的应用场景，代码维护较为复杂，但是缓存空间占用少。

​        布隆过滤器实际上是一个很长的二进制向量和一系列随机映射函数。布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。

算法描述：

- 初始状态时，BloomFilter是一个长度为m的位数组，每一位都置为0。
- 添加元素x时，x使用k个hash函数得到k个hash值，对m取余，对应的bit位设置为1。
- 判断y是否属于这个集合，对y使用k个哈希函数得到k个哈希值，对m取余，所有对应的位置都是1，则认为y属于该集合（哈希冲突，可能存在误判），否则就认为y不属于该集合。可以通过增加哈希函数和增加二进制位数组的长度来降低错报率。


![](https://pic.imgdb.cn/item/65a94f71871b83018af432f6.jpg)
**错报原因：**

​     一个key映射数组上多位，一位会被多个key使用，也就是多对多的关系。如果一个key映射的所有位值为1，就判断为存在。但是可能会出现key1 和  key2 同时映射到下标为100的位，key1不存在，key2存在，这种情况下会发生错误率


![](https://pic.imgdb.cn/item/65a94f89871b83018af49bff.jpg)
## 3、缓存雪崩

​       由于缓存层承载着大量请求，有效地保护了存储层，但是如果缓存层由于某些原因不可用（宕机）或者大量缓存由于超时时间相同在同一时间段失效（大批key失效/热点数据失效），大量请求直接到达存储层，存储层压力过大导致系统雪崩。

## 4、解决方案：

​       可以把缓存层设计成高可用的，即使个别节点、个别机器、甚至是机房宕掉，依然可以提供服务。利用sentinel或cluster实现。
​       采用多级缓存，本地进程作为一级缓存，redis作为二级缓存，不同级别的缓存设置的超时时间不同，即使某级缓存过期了，也有其他级别缓存兜底
​       缓存的过期时间用随机值，尽量让不同的key的过期时间不同（例如：定时任务新建大批量key，设置的过期时间相同）

## 5、缓存击穿

​       系统中存在以下两个问题时需要引起注意：

- 当前key是一个热点key（例如一个秒杀活动），并发量非常大。
- 重建缓存不能在短时间完成，可能是一个复杂计算，例如复杂的SQL、多次IO、多个依赖等。

​       在缓存失效的瞬间，有大量线程来重建缓存，造成后端负载加大，甚至可能会让应用崩溃。

## 6、解决方案：

### 6.1分布式互斥锁

只允许一个线程重建缓存，其他线程等待重建缓存的线程执行完，重新从缓存获取数据即可。set(key,value,timeout)

![](https://pic.imgdb.cn/item/65a94fa6871b83018af51b4f.jpg)


### **6.2. 永不过期**

- 从缓存层面来看，确实没有设置过期时间，所以不会出现热点key过期后产生的问题，也就是“物理”不过期。
- 从功能层面来看，为每个value设置一个逻辑过期时间，当发现超过逻辑过期时间后，会使用单独的线程去更新缓


![](https://pic.imgdb.cn/item/65a94fbf871b83018af58bd0.jpg)
**2种方案对比：**

- 分布式互斥锁：这种方案思路比较简单，但是存在一定的隐患，如果在查询数据库 + 和 重建缓存（key失效后进行了大量的计算）时间过长，也可能会存在死锁和线程池阻塞的风险，高并发情景下吞吐量会大大降低！但是这种方法能够较好地降低后端存储负载，并在一致性上做得比较好。
- “永远不过期”：这种方案由于没有设置真正的过期时间，实际上已经不存在热点key产生的一系列危害，但是会存在数据不一致的情况，同时代码复杂度会增大。



### **Redis如何保证数据不丢失。以下是几种常用的方式：**

快照持久化：Redis可以通过将内存中的数据快照写入磁盘来实现持久化。这种方式下，Redis会周期性地将内存中的数据写入磁盘，以防止系统故障导致数据丢失。可以通过配置Redis的持久化策略来控制快照的频率。

AOF持久化：在AOF模式下，Redis会将每个写操作追加到一个日志文件中，当日志文件发生改变时候，则会自动触发。那么Redis重启时，可以通过重新执行日志文件中的写操作来恢复数据。AOF持久化相对于快照持久化来说，可以提供更高的数据安全性，但也会带来更高的写入延迟和文件大小。