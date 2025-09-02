---
title: typescript
date: 2024-08-12 22:06:37
comments: true #是否可评论
tags: # 文章的标签
	- typescript
	- ts
	- js
categories: 
	- typescript
description: TypeScript 是 JavaScript 的超集，扩展了 JavaScript 的语法，因此现有的 JavaScript 代码可与 TypeScript 一起工作无需任何修改，TypeScript 通过类型注解提供编译时的静态类型检查。TypeScript 可处理已有的 JavaScript 代码，并只对其中的 TypeScript 代码进行编译。	  
top_img: https://pic.imgdb.cn/item/66ba179dd9c307b7e950c8fa.png     #顶部图片
cover: https://pic.imgdb.cn/item/66ba179dd9c307b7e950c8fa.png    #主页中显示的图片
---





##### 3.4数组类型

数组类型的两种写法：（推荐使用number[]写法）

```typescript
let numbers :number[]=[1,3,5]
let strings :Array<string> =['a','b','c']
```

需求：数组中既有number类型，又有string类型，这个数组的类型应该如何写？

```typescript
let arr :(number|string)[] = [1,'a',3,'b']//小括号一定要有

//如果去掉括号的话就变成
let arr :number|string[] = [1,'a',3,'b']//语法错误！！！只能放number类型或sring类型的数组。注意此时写成let arr :number|string[] = [1,3] 也是会报错的，此时的number不是数组类型。正确的为  let arr :number|string[] = [123] 或者 let arr :number|string[] = ['a','b']
```

##### 3.5类型别名

类型别名：为任意类型起别名

使用场景：当同一类型（复杂）被多次使用时，可以通过类型别名，简化该类型的使用
![](https://pic.imgdb.cn/item/66ba192fd9c307b7e955f302.png)


解释：

1. 使用type关键字创建类型别名
2. 创建类型别名后，直接使用该类型别名作为变量的类型注解即可

##### 3.6函数类型

函数的类型实际上指的是：函数***参数***和***返回值***的类型

为函数指定类型的两种方式：

1.单独指定参数、返回值的类型    

![](https://pic.imgdb.cn/item/66ba194fd9c307b7e9561917.png)

2.同时指定参数、返回值的类型（这个写法了解就行）

![](https://pic.imgdb.cn/item/66ba1965d9c307b7e9562e97.png)

如果函数没有返回值，那么返回值类型为：void

![](https://pic.imgdb.cn/item/66ba198bd9c307b7e95653a4.png)



如果函数实现某个功能时，参数可以传也可以不传。这种情况下，在给函数参数指定类型时，就用到可选参数了，比如 数组slice方法，可以是slice()也可以slice(1)还可以slice(1,3)。

解释：

可选参数：在可传可不传的参数名称后面添加❓

注意：可选参数只能出现在参数列表的最后，也就是说可选参数后面不能再出现必须参数。


![](https://pic.imgdb.cn/item/66ba19a7d9c307b7e956737e.png)

##### 3.7对象类型

JS中的对象是由属性和方法构成的，而TS中对象的类型就是在描述对象的结构。
![](https://pic.imgdb.cn/item/66ba19bbd9c307b7e9568a34.png)


解释：

1. 直接使用{}来描述对象结构。属性采用属性名:类型的形式；方法采用方法名():返回值类型的形式。
2. 如果方法有参数，就在方法名后面的小括号中指定参数类型。比如greet(name: string) :void
3. 在一行代码中指定对象的多个属性类型时，使用;来分隔。如果一行代码只指定一个类型属性，可以去掉;就连,都不用添加

```typescript
let person:{name: string
     age:number
     sayHi():void
     greet(name: string) :void
    } = {
    name: 'zhangsan',
    age: 18,
    sayHi() {
        
    },
    greet(name) {
        
    },
}
```

对象的属性或方法，也是可选的，此时就用到**可选属性**了

比如，我们在使用axios({...})时，如果发送GET请求，method属性就可以省略。

![](https://pic.imgdb.cn/item/66ba19d6d9c307b7e956a947.png)

##### 3.8接口

当一个对象类型被多次使用时，一般会使用接口(interface) 来描述对象的类型，达到复用的目的。


![](https://pic.imgdb.cn/item/66ba1a12d9c307b7e956ed53.png)

interface(接口)和type(类型别名)的对比：

- 相同点：都可以给对象指定类型
- 不同点
  - 接口，只能为对象指定类型。
  - 类型别名，不仅可以为对象指定类型，还可以为任意类型指定别名。

![](https://pic.imgdb.cn/item/66ba1ae6d9c307b7e957d86d.png)

如果两个接口之间有相同的属性或方法，可以将公共 的属性或方法抽离出来，通过继承来实现复用。

![](https://pic.imgdb.cn/item/66ba1b50d9c307b7e9585720.png)
