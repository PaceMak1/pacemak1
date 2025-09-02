---
title: 支付宝沙箱支付
date: 2023-10-17 22:15:46
comments: true #是否可评论
tags: # 文章的标签
	- 支付宝
	- 沙箱
categories: 
	- 支付宝
description: 对于调用支付宝API笔记	  
top_img: https://pic.imgdb.cn/item/65a7e1c6871b83018a6b60f8.jpg     #顶部图片
cover: https://pic.imgdb.cn/item/65a7e1c6871b83018a6b60f8.jpg    #主页中显示的图片
---


# 一、生成秘钥
#### 1.1 登录平台
登录支付宝开放平台：https://open.alipay.com/
完善账户信息后;
1)点击控制台：

![](https://pic.imgdb.cn/item/65a7e246871b83018a6de77f.jpg)

2)找到开发工具推荐，控制台下方的沙箱：
![](https://pic.imgdb.cn/item/65a7e25b871b83018a6e4c3d.jpg)



3)进入如下页面；

![](https://pic.imgdb.cn/item/65a7e28a871b83018a6f3c38.jpg)

####  1.2 生成秘钥
注意：生成秘钥方式只要选择一种即可，“系统默认秘钥”比较简单，自定义秘钥需要使用工具生成；
####  1.2.1 系统默认秘钥
（这是第一种方式）

![](https://pic.imgdb.cn/item/65a7e2be871b83018a703645.jpg)


点击“查看”，可以看到如下界面；
这个 “应用私钥” 和 “支付宝公钥” 在之后的步骤中需要配置到Java程序


![](https://pic.imgdb.cn/item/65a7e2e0871b83018a70e089.jpg)


#### 1.2.2 自定义秘钥
这是第二种方式：这个步骤需要生成工具！！！
二选一即可，此种方式暂时可以不用

![](https://pic.imgdb.cn/item/65a7e304871b83018a7195d5.jpg)

点击“自定义秘钥”

![](https://pic.imgdb.cn/item/65a7e31a871b83018a7203bc.jpg)

下载秘钥生成工具

![](https://pic.imgdb.cn/item/65a7e331871b83018a72752f.jpg)

![](https://pic.imgdb.cn/item/65a7e342871b83018a72c9d3.jpg)
安装成功之后，点击生成秘钥


![](https://pic.imgdb.cn/item/65a7e360871b83018a735be8.jpg)

![](https://pic.imgdb.cn/item/65a7e373871b83018a73c3cb.jpg)

![](https://pic.imgdb.cn/item/65a7e388871b83018a742917.jpg)


回到沙箱应用，填入公钥并确定完成；
![](https://pic.imgdb.cn/item/65a7e3a1871b83018a74a416.jpg)


注意：↑ 这个页面的网关地址和授权回调地址填入：https://www.alipay.com
点击上图的"设置并查看" 填入自己本地的“应用公钥"，就可以得到”支付宝公钥“；
这个”支付宝公钥“ 和 保存在我们本地的“应用私钥” 就是我们要配置到Java程序中的。


![](https://pic.imgdb.cn/item/65a7e3bd871b83018a752a48.jpg)

在沙箱账号中支付宝给我们提供了商家和买家账号：在支付环节需要用到

![](https://pic.imgdb.cn/item/65a7e3d5871b83018a75a8d1.jpg)


1.3 开发文档
可以参考官方文档进行调试（此步非必须，可以直接跳到 二、配置环境）
[](https://open.alipay.com/api)


![](https://pic.imgdb.cn/item/65a947a3871b83018ad18691.jpg)

![](https://pic.imgdb.cn/item/65a947be871b83018ad20013.jpg)

![](https://pic.imgdb.cn/item/65a947cf871b83018ad2462f.jpg)




## 二、配置环境
#### 2.1 沙箱环境准备
登录沙箱地址
[](https://open.alipay.com/develop/sandbox/app)
保证接口加签方式已经启用：
![](https://pic.imgdb.cn/item/65a9480a871b83018ad3543f.jpg)




官方文档
[](https://opendocs.alipay.com/open/270/01didh)
账号准备
![](https://pic.imgdb.cn/item/65a94830871b83018ad406f3.jpg)



2.2 内网穿透工具
[](https://natapp.cn/)
注册一个账号，后期会要求实名认证，照做即可。

![](https://pic.imgdb.cn/item/65a94862871b83018ad4da44.jpg)

购买穿透隧道，可以选择免费一个月的。

![](https://pic.imgdb.cn/item/65a9487d871b83018ad55676.jpg)

![](https://pic.imgdb.cn/item/65a94893871b83018ad5bbb5.jpg)
购买成功后看下面的列表，点击配置：
![](https://pic.imgdb.cn/item/65a948af871b83018ad6397f.jpg)


会有生成认证令牌：复制并保存
注意：端口改为此时正在启动服务的端口，比如现在IDEA的Springboot项目中 tomcat使用的是9999端
口，则此时这里本地端口要改为9999

![](https://pic.imgdb.cn/item/65a948cd871b83018ad6babd.jpg)
在官网右上角点击下载客户端工具: 根据你电脑情况选择合适的版本下载，这里我选用Windows64位



![](https://pic.imgdb.cn/item/65a948e2871b83018ad70f73.jpg)
客户端启动需要一个config.ini文件，放到和下载的客户端natapp.exe文件同一个文件夹下，authtoken
的值粘贴为之前这里复制的令牌：


![](https://pic.imgdb.cn/item/65a94906871b83018ad7ae41.jpg)
![](https://pic.imgdb.cn/item/65a94911871b83018ad7df5f.jpg)
启动客户端：windows下,直接双击natapp.exe 即可。红框内就是我们的隧道通信地址。

![](https://pic.imgdb.cn/item/65a94926871b83018ad8429d.jpg)

注意：每次启动客户端都会分配一个新的隧道地址：要注意后期更换程序中配置 的地址。
测试：此时 这个穿透地址是临时域名就是代表了本地应用程序的访问地址http://localhost:9999
可以直接使用此域名来访问应用程序下的任意一个接口，如能访问，则环境OK。
比如： 我现在springboot应用启动了，那么输入这个临时域名 + 访问资源路径：
http://f55v9f.natappfree.cc/user/findAll 查询所有用户，能正常访问，就是测试成功！

![](https://pic.imgdb.cn/item/65a94951871b83018ad904d7.jpg)



# 三、Java程序配置
#### 3.1 添加依赖
刷新maven，直到把资源下载下来为止。 也可以选择其他版本
```
<dependency>
<groupId>com.alipay.sdk</groupId>
<artifactId>alipay-sdk-java</artifactId>
<version>4.9.13.ALL</version>
</dependency>
```

#### 3.2 配置 application.yml
增加如下信息：

```
alipay:
appId: 这里配置的是“沙箱应用”中的APPID号,如下图
appPrivateKey: 配置应用私钥，之前有获得过
alipayPublicKey: 配置“支付宝公钥”，之前有获得过，注意：不是“应用公钥”
notifyUrl: 你自己的隧道通信地址/alipay/notify
# 比如我这里配置为： http://muaqx9.natappfree.cc/alipay/notify
```


![](https://pic.imgdb.cn/item/65a949a6871b83018ada87dc.jpg)

配置成这样：
![](https://pic.imgdb.cn/item/65a949ba871b83018adadf8f.jpg)

#3.3 编写Java程序
1）新Alipay类，用于前后端交互，注意加上@Data注解（或者自行加上 gettersetter 也可以）

``` java
/**
* @ClassName 和阿里交互的对象（用于前端和后端交互，字段名最好严格按照规范）
* @Author 小温哥 @Version 1.0
* 功能说明：
**/
@Data
public class AliPay {
private String traceNo; //我们的订单号
private Double totalAmount; //总金额
private String subject; //主题
private String alipayTraceNo;//阿里的流水号
}
```

2）新增AlipayConfig配置类，增加如下注解，读取yaml中关于alipay的配置信息
``` java
/**
* @ClassName
* @Author 小温哥 @Version 1.0
* 功能说明：
**/
@Data
@Component
@ConfigurationProperties(prefix = "alipay")
public class AlipayConfig {
private String appId;
//应用私钥
private String appPrivateKey;
//阿里公钥
private String alipayPublicKey;
//阿里调用我们的地址【内网穿透】
private String notifyUrl;
}

```



如果这个类的上方有警告关于注解处理器配置问题，则增加依赖并刷新maven:
```
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-configuration-processor</artifactId>
</dependency>
```


3）编写控制器类AlipayController

``` java
package com.cykj.controll;
import com.alibaba.fastjson.JSONObject;
import com.alipay.api.AlipayApiException;
import com.alipay.api.AlipayClient;
import com.alipay.api.DefaultAlipayClient;
import com.alipay.api.internal.util.AlipaySignature;
import com.alipay.api.request.AlipayTradePagePayRequest;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
/**
* @ClassName
* @Author 小温哥 @Version 1.0
* 功能说明：
**/
@RestController
@RequestMapping("/alipay")
public class AliPayController {
//阿里网关地址
private static final String GATEWAY_URL =
"https://openapi.alipaydev.com/gateway.do";
//数据格式
private static final String FORMAT = "JSON";
//编码格式
private static final String CHARSET = "UTF-8";
//签名方式
private static final String SIGN_TYPE = "RSA2";
@Resource
private AlipayConfig alipayConfig;


@GetMapping("/pay") // ?subject=xxx&traceNo=xxx&totalAmount=xxx
public void pay(AliPay aliPay, HttpServletResponse response) throws
IOException {
System.out.println(aliPay);
System.out.println("alipayConfig:" + alipayConfig);
//1.创建client，通过阿里SDK提供的client，负责调用支付宝的API
AlipayClient alipayClient = new DefaultAlipayClient(GATEWAY_URL,
alipayConfig.getAppId(),
alipayConfig.getAppPrivateKey(),
FORMAT,
CHARSET,
alipayConfig.getAlipayPublicKey(),
SIGN_TYPE);
//2.创建request，并设置request参数
AlipayTradePagePayRequest request = new AlipayTradePagePayRequest();
request.setNotifyUrl(alipayConfig.getNotifyUrl());
JSONObject bizContent = new JSONObject();
bizContent.put("out_trade_no", aliPay.getTraceNo());//我们自己生成的订单编号
bizContent.put("total_amount", aliPay.getTotalAmount());//订单总金额
bizContent.put("subject", aliPay.getSubject());//支付名称
bizContent.put("product_code", "FAST_INSTANT_TRADE_PAY");//固定配置
request.setBizContent(bizContent.toString());
//执行请求，拿到响应的结果，返回给浏览器
String form = "";
try {
form = alipayClient.pageExecute(request).getBody();//调用SDK生成表单
} catch (AlipayApiException e) {
e.printStackTrace();
}
//设置响应结果，将返回的内容写出到浏览器
response.setContentType("text/html;charset=" + CHARSET);
response.getWriter().write(form);//直接将完整的表单html输出到页面
response.getWriter().flush();
response.getWriter().close();
}
/**
* 支付宝异步回调【必须是POST】
*
* @return
*/
@PostMapping("/notify")
public String payNotify(HttpServletRequest request) throws
AlipayApiException {
System.out.println("异步回调了。" + request);
if (request.getParameter("trade_status").equals("TRADE_SUCCESS")) {
System.out.println("-------------支付宝异步回调----");
Map<String, String> params = new HashMap<>();
Map<String, String[]> requestParams = request.getParameterMap();
System.out.println("-----params-----------");
for (String name : requestParams.keySet()) {
params.put(name, request.getParameter(name));
System.out.println(name + " " + request.getParameter(name));
}
String outTradeNo = params.get("out_trade_no");
String gmtPayment = params.get("gmt_payment");//支付时间
String alipayTradeNo = params.get("trade_no");
String sign = params.get("sign");//拿到签名
String content = AlipaySignature.getSignCheckContentV1(params);
boolean checkSignature = AlipaySignature.rsa256CheckContent(content,
sign,
alipayConfig.getAlipayPublicKey(),
"UTF-8");//验证签名
//支付宝验签
if (checkSignature) {
//验签通过
System.out.println("交易名称: " + params.get("subject"));
System.out.println("交易状态: " + params.get("trade_status"));
System.out.println("支付宝交易凭证号: " + params.get("trade_no"));
System.out.println("商户订单号: " + params.get("out_trade_no"));
System.out.println("交易金额: " + params.get("total_amount"));
System.out.println("买家在支付宝唯一id: " +
params.get("buyer_id"));
System.out.println("买家付款时间: " + params.get("gmt_payment"));
System.out.println("买家付款金额: " +
params.get("buyer_pay_amount"));
// 查询订单
//TODO 支付成功，操作数据库，创建对应订单，修改对应商品数据
}
}
return "success";
}
}






```


# 四、前端程序编写
#### 新建Alipay.vue，并且给他配置对应的路由。
#### 因为支付宝给我们返回的数据是一个form表单，所以我们直接以form表单中的a标签进行提交：

```html

<template>
<div>
<h3>测试支付宝支付</h3>
<el-form label-width="80px" :model="aliPay">
<el-form-item label="订单id">
<el-input v-model="aliPay.traceNo" ></el-input>
</el-form-item>
<el-form-item label="订单价格">
<el-input v-model="aliPay.totalAmount" ></el-input>
</el-form-item>
</el-form>
<a :href="payURL">确定支付</a>
</div>
</template>
<script>
export default {
	data() {
	return {
	// 提交的流水和金额
	aliPay: {
	traceNo: "",
	totalAmount: 0.0
	},
	};
	},
	// 计算属性
	computed:{
	payURL:function(){
	return "http://localhost:9999/alipay/pay?subject=收购阿里&traceNo=" +
	this.aliPay.traceNo + "&totalAmount=" + this.aliPay.totalAmount
	}
	}
	};
	</script>
```


点击支付：
![](https://pic.imgdb.cn/item/65a94aec871b83018ae02241.jpg)

![](https://pic.imgdb.cn/item/65a94b45871b83018ae1d452.jpg)


# 五、沙箱支付宝付款
####沙箱环境是模拟环境，所以要使用沙箱版支付宝进行支付，下载安装：

此时回到 
[](https://open.alipay.com/develop/sandbox/app)\

![](https://pic.imgdb.cn/item/65a94b86871b83018ae2f199.jpg)

![](https://pic.imgdb.cn/item/65a94b9e871b83018ae359ba.jpg)

回到沙箱账号充钱！！

![](https://pic.imgdb.cn/item/65a94bbb871b83018ae3e7c0.jpg)

打开沙箱支付宝客户端，输入买家账号和密码，
打开“沙箱支付宝”， 登录方式选择“ 其他方式登录”

![](https://pic.imgdb.cn/item/65a94bc9871b83018ae4274f.jpg)


![](https://pic.imgdb.cn/item/65a94bd5871b83018ae45a12.jpg)

然后扫支付二维码：
![](https://pic.imgdb.cn/item/65a94bf0871b83018ae4d2b4.jpg)



![](https://pic.imgdb.cn/item/65a94bfe871b83018ae50a8a.jpg)


![](https://pic.imgdb.cn/item/65a94c0f871b83018ae5571f.jpg)