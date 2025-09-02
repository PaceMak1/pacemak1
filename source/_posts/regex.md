---
title: 正则表达式
date: 2022-05-05 10:48:52
comments: true #是否可评论
tags: # 文章的标签
	- 正则表达式
categories: 
	- 正则表达式
description: 一个正则表达式，就是用某种模式去匹配字符串的一个公式。	  
top_img: https://pic.imgdb.cn/item/656e9057c458853aef5ae5be.jpg    #顶部图片
cover: https://pic.imgdb.cn/item/656e9057c458853aef5ae5be.jpg    #主页中显示的图片

---

#### 1.快速入门

提取英文

![](https://pic.imgdb.cn/item/656e910cc458853aef5c8c79.jpg)


提取数字
![](https://pic.imgdb.cn/item/656e9128c458853aef5cd008.jpg)


提取英文和数字
![](https://pic.imgdb.cn/item/656e9142c458853aef5d17d6.jpg)


#### 2.源码分析：

1.正则表达式中没有分组的情况

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegTheory {
    public static void main(String[] args) {
        String content="1998年12月8日，第二代Java平台的企业版J2EE发布。1999年6月，Sun公司发布了第二代Java平台" +
                "（简称为Java2）的3个版本：J2ME（Java2 Micro Edition，Java2平台的微型版），应用于移动、" +
                "无线及有限资源的环境；J2SE（Java 2 Standard Edition，Java 2平台的标准版），应用于桌面环境；" +
                "J2EE（Java 2Enterprise Edition，Java 2平台的企业版），应用于基于Java的应用服务器。Java 2平台的发布，" +
                "是Java发展过程中最重要的一个里程碑，标志着Java的应用开始普及。";
//目标：匹配所有四个数字
//        说明
//       1. \\d 表示一个任意数字
        String regStr="\\d\\d\\d\\d";
//        创建模式对象[即正则表达式对象]
        Pattern pattern = Pattern.compile(regStr);
//        创建匹配器matcher,按照正则表达式，去匹配content
        Matcher matcher = pattern.matcher(content);
        /**
         * 开始匹配
         * matcher.find() 所做的任务是：
         * 1.根据指定的规则，定位满足规则的子字符串(比如1998)
         * 2.找到后，将 子字符串的开始的索引记录到matcher对象中的属性      int[] groups   中;
         *          此时groups[0]=0,把该子字符串的结束的索引+1的值记录到groups[1]=4
         * 3.同时记录oldLast的值为，子该子字符串的结束的索引+1的值即4，那么下次执行find方法时，就从4开始匹配
         *
         * matcher.group(0)分析
         *
         * 源码
         *     public String group(int group) {
         *         if (first < 0)
         *             throw new IllegalStateException("No match found");
         *         if (group < 0 || group > groupCount())
         *             throw new IndexOutOfBoundsException("No group " + group);
         *         if ((groups[group*2] == -1) || (groups[group*2+1] == -1))
         *             return null;
         *         return getSubSequence(groups[group * 2], groups[group * 2 + 1]).toString();    看这里
         *     }
         *  1.根据groups[0]=0 和 groups[1]=4 记录的位置，从content开始截取子字符串返回。
         *      因为通过subString截取出来的时候是包含0，但是不包含索引为4这个位置的，就是[0,4)
         *
         *      String str ="abcdef";
         *      执行str.substring(0,4)的结果为 abcd
         *-----------------------------------------------------------
         *
         * 如果再次执行find方法，仍然按上面的进行分析:
         *
         * 1.根据指定的规则，定位满足规则的子字符串(比如1996)
         * 2.找到后，将 子字符串的开始的索引记录到matcher对象中的属性      int[] groups   中;
         *          此时groups[0]=31,把该子字符串的结束的索引+1的值记录到groups[1]=35
         * 3.同时记录oldLast的值为，子该子字符串的结束的索引+1的值即35，那么下次执行find方法时，就从35开始匹配
         *
         *  1.根据groups[0]=31 和 groups[1]=35 记录的位置，从content开始截取子字符串返回。
         *      因为通过subString截取出来的时候是包含31，但是不包含索引为35这个位置的，就是[31,35)
         */
        while (matcher.find()){
            System.out.println("找到了"+matcher.group(0));
        }
    }
}
```



2.正则表达式中存在分组的情况

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegTheory {
    public static void main(String[] args) {
        String content="1998年12月8日，第二代Java平台的企业版J2EE发布。1999年6月，Sun公司发布了第二代Java平台" +
                "（简称为Java2）的3个版本：J2ME（Java2 Micro Edition，Java2平台的微型版），应用于移动、" +
                "无线及有限资源的环境；J2SE（Java 2 Standard Edition，Java 2平台的标准版），应用于桌面环境；" +
                "J2EE（Java 2Enterprise Edition，Java 2平台的企业版），应用于基于Java的应用服务器。Java 2平台的发布，" +
                "是Java发展过程中最重要的一个里程碑，标志着Java的应用开始普及。";
//目标：匹配所有四个数字
//        说明
//       1. \\d 表示一个任意数字
        String regStr="(\\d\\d)(\\d\\d)";
//        创建模式对象[即正则表达式对象]
        Pattern pattern = Pattern.compile(regStr);
//        创建匹配器matcher,按照正则表达式，去匹配content
        Matcher matcher = pattern.matcher(content);
        /**
         * 开始匹配
         * matcher.find() 所做的任务是：(考虑分组)
         * 什么是分组，比如(\d\d)(\d\d)，正则表达式中有()表示分组，第一个()表示第1组，第二个()表示第2组，第三个()表示第3组.........
         *
         * 1.根据指定的规则，定位满足规则的子字符串(比如1998)
         * 2.找到后，将 子字符串的开始的索引记录到matcher对象中的属性      int[] groups   中;
         *          2.1此时groups[0]=0,把该子字符串的结束的索引+1的值记录到groups[1]=4
         *          2.2 记录第1组()匹配到的字符串  groups[2]=0   groups[3]=1+1     分组记录的值，结束的索引也要加+,还是substring的规则。
         *          2.3 记录第2组()匹配到的字符串  groups[4]=2   groups[5]=3+1
         * 3.同时记录oldLast的值为，子该子字符串的结束的索引+1的值即4，那么下次执行find方法时，就从4开始匹配
         *
         * matcher.group(0)分析
         *
         * 源码
         *     public String group(int group) {
         *         if (first < 0)
         *             throw new IllegalStateException("No match found");
         *         if (group < 0 || group > groupCount())
         *             throw new IndexOutOfBoundsException("No group " + group);
         *         if ((groups[group*2] == -1) || (groups[group*2+1] == -1))
         *             return null;
         *         return getSubSequence(groups[group * 2], groups[group * 2 + 1]).toString();    看这里!!!!
         *     }
         *  1.根据groups[0]=0 和 groups[1]=4 记录的位置，从content开始截取子字符串返回。
         *      因为通过subString截取出来的时候是包含0，但是不包含索引为4这个位置的，就是[0,4)
         *
         *      String str ="abcdef";
         *      执行str.substring(0,4)的结果为 abcd
         *-----------------------------------------------------------
         *
         * 如果再次执行find方法，仍然按上面的进行分析:
         *
         * 1.根据指定的规则，定位满足规则的子字符串(比如1996)
         * 2.找到后，将 子字符串的开始的索引记录到matcher对象中的属性      int[] groups   中;
         *          此时groups[0]=31,把该子字符串的结束的索引+1的值记录到groups[1]=35
         * 3.同时记录oldLast的值为，子该子字符串的结束的索引+1的值即35，那么下次执行find方法时，就从35开始匹配
         *
         *  1.根据groups[0]=31 和 groups[1]=35 记录的位置，从content开始截取子字符串返回。
         *      因为通过subString截取出来的时候是包含31，但是不包含索引为35这个位置的，就是[31,35)
         */
        while (matcher.find()){
            /**
             * 小结：
             * 1.如果正则表达式有()分组
             * 2.取出匹配的字符串规则如下
             * 3.group(0)表示匹配到的子字符串
             * 4.group(1)表示匹配到的子字符串的第一组
             * 5.group(2)表示匹配到的子字符串第二组
             * 6.但是分组数不能越界。
             */
            System.out.println("找到了"+matcher.group(0));
            System.out.println("第一组小括号取到的值"+matcher.group(1));
            System.out.println("第二组小括号取到的值"+matcher.group(2));
        }
    }
}

```



#### 3.正则表达式语法

3.1元字符-转义符\\
```java
需要用到转义符号的字符有：  . * + ( ) $ / \ ? [ ] ^ { }
```

3.2元字符-字符匹配符
	
| 符号  | 含义                                                      | 示例           | 说明                                                         |
| ----- | --------------------------------------------------------- | -------------- | ------------------------------------------------------------ |
| [ ]   | 可接收的字符列表                                          | [efgh]         | e、f、g、h中的***<u>任意一个</u>***字符                      |
| [ ^ ] | 不接收的字符列表                                          | [^abc]         | 除a、b、c之外的***<u>任意一个</u>***字符，包括数字和特殊符号 |
| -     | 连字符                                                    | A-Z            | 任意单个大写字母                                             |
| .     | 匹配除\n 以外的任何字符。如果要匹配.本身那就需要使用\\\\. | a..b           | 以a开头，b结尾，中间包括2个任意字                            |
| \\\d  | 匹配单个数字字符，相当于[0-9]                             | \\\d{3}(\\\d)? | 包含3个或4个数字的字符串                                     |
| \\\D  | 匹配单个非数字字符，相当于\[^0-9]                         | \\\D(\\\d)*    | 以单个非数字字符开头，后接任意个数字字符串                   |
| \\\w  | 匹配单个数字、大小写字母、下划线字符,相当于[0-9a-zA-Z]    | \\\d{3}\\\w{4} | 以3个数字字符开头的,长度为7的数字字母字符串                  |
| \\\W  | 匹配单个非数字、大小写字母字符，相当于\[^0-9a-zA-Z]       | \\\W+\\\d{2}   | 以至少1个非数字字母字符开头，2个数字字符结尾的字符串         |
| \\\s  | 匹配任何空白字符(空格、制表符等)                          |                |                                                              |
| \\\S  | 匹配任何非空白字符(空格、制表符等)                        |                |                                                              |


​	Java正则表达式默认是区分字母大小写的，如何实现不区分大小写？

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegExp03 {
    public static void main(String[] args) {
        String content ="abc1123ABC";
//        String regStr = "abc";//1.匹配abc字符串  默认区分大小写
//        String regStr = "(?i)abc";//2.匹配abc字符串  不区分大小写
//        String regStr = "a(?i)bc";//3.匹配abc字符串  b、c不区分大小写
        String regStr = "a((?i)b)c";//4.匹配abc字符串  只有b不区分大小写
        Pattern pattern = Pattern.compile(regStr);
        Matcher matcher = pattern.matcher(content);
        while (matcher.find()){
            System.out.println("找到了"+ matcher.group(0));
        }
    }
}
```
或者：在创建正则表达式对象的时候就注入参数      (INSENSITIVE   不敏感的)
```java
Pattern pattern = Pattern.compile(regStr,Pattern.CASE_INSENSITIVE);
```



3.3元字符-限定符

用于指定  <font color="red">其前面的字符</font>  且只有距离最接近的符号才会生效，如果要多个符号生效就需要括号包含）和组合项连续出现多少次。

| 符号  |                 含义                  |    示例     |                       说明                        |        匹配输入         |
| :---: | :-----------------------------------: | :---------: | :-----------------------------------------------: | :---------------------: |
|   *   |  指定字符重复0次或n次(无要求)  0到多  |   (abc)*    |              仅包含任意个abc的字符串              |     abc、abcabcabc      |
|   +   | 指定字符重复1次或n次(至少一次)  1到多 |  m+(abc)*   |      以至少一个m开头，后接任意个abc的字符串       |   m、mabcabc、mabcabc   |
|   ?   |   指定重读0次或1次(最多一次)  0到1    |   m+abc?    |        以至少1个m开头，后接ab或abc的字符串        | mab、mabc、mmmab、mmabc |
|  {n}  |            只能输入n个字符            |  [abcd]{3}  |       由abcd中字母组成的任意长度为3的字符串       |      abc、dbc、adc      |
| {n,}  |            指定至少n个匹配            | [abcd]{3,}  |     由abcd中字母组成的任意长度不小于3的字符串     | aab、abcd、aaaaa、bcdab |
| {n,m} |      指定至少n个但不多于m个匹配       | [abcd]{3,5} | 由abcd中字母组成的任意长度不小于3,不大于5的字符串 | abc、abcd、aaaaa、bcdab |






3.4元字符-定位符

定位符，<font color="red">规定要匹配的字符串出现的位置</font> ，比如在字符串的开始还是结束的位置。

| 符号 |          含义          |        示例        |                             说明                             |            匹配输入             |
| :--: | :--------------------: | :----------------: | :----------------------------------------------------------: | :-----------------------------: |
|  ^   |      指定开始字符      |   ^[0-9]+[a-z]*    | 以至少1个数字开头、后接任意个小写字母的<font color="red">字符串</font>，这是判断整个字符串，不是单个字符判断 |        123、6aa、555edf         |
|  $   |      指定结束字符      | ^[0-9]\\\\-[a-z]+$ | 以至少1个数字开头后接连字符"-"  并以至少1个小写字母结尾的字符串 |               1-a               |
| \\\b |  匹配目标字符串的边界  |      han\\\b       | 这里说的字符串的边界指的是被匹配的字符串的最后或者是空格的子字符串的后面，<font color="red">开头的不算</font> | hanshunping sp<font color="red">han</font> nn<font color="red">han</font> |
| \\\B | 匹配目标字符串的非边界 |      han\\\B       |                       和\\\b的含义相反                       |   <font color="red">han</font>shunping sphan nnhan   |





### 4.Matcher类
![](https://pic.imgdb.cn/item/65a12865871b83018a9500cc.jpg)


### 5.反向引用：

抛出需求：给你一段文本，请你找出所有四个数字连在一起的子串，并且这四个数字要满足 

- 第一位与第四位相同
- 第二位与第三位相同
- 比如：1221 ，5775

圆括号的内容被捕获后，可以在这个括号后被使用，从而写出一个比较实用的匹
配模式，这个我们称为反向引用,这种引用既可以是在正则表达式内部，也可以是
在正则表达式外部，**内部反向引用**\\\分组号，**外部反向**引用 $分组号

```java
//要匹配两个连续的相同数字:       (\\d)\\1
//要匹配五个连续的相同数字:        (\\d)\\1{4}
//要匹配个位与千位相同，十位与百位相同的数:   5225,1551   (\\d)(\\d)\\2\\1      \\2表示反向引用第2个分组中的数据    
```




String类中正则表达式的使用:

```java
public String replaceAll(String regex,String replacement);
```
![](https://pic.imgdb.cn/item/65a12879871b83018a9556a6.jpg)

```java
public boolean matches(String regex);
```
![](https://pic.imgdb.cn/item/65a12884871b83018a9586a5.jpg)


```java
public String[] split(String regex);
```
![](https://pic.imgdb.cn/item/65a12893871b83018a95c536.jpg)