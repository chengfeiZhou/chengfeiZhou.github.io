---
title: Java学习之绪论
date: 2021-01-10 15:58:36
tags:
    - Java
categories:
    - Java
    - 基础班
    - 绪论
---

# 一、开发前言:
## 1. java语言概述:
### 1.1 什么是java语言:
...
### 1.2 java发展史:
...

### 1.3 java语言能做什么:
...
## 2. 计算机基础知识:
### 2.1 二进制:
二进制转十进制： ”8421法“;
十进制转二进制: "辗转相除法";
### 2.2 存储单元:
1 Byte(字节) = 8bit(位,比特)
1 KB = 1024 Byte
1 MB = 1024 KB
1 GB = 1024 MB
1 TB = 1024 GB
1 PB = 1024 TB
1 EB = 1024 PB
1 ZB = 1024 EB
**注:**
- 进制:
1024 = 2^10^
- 一般网络带宽:
100Mbps =>100Mbps /8 = 12.5MB/s

## 3.windows的命令提示符:
(我不是第一次使用电脑)


# 二、java开发环境搭建:
## 1. java虚拟机 -- JVM:
- jvm: java的运行环境;
- 跨平台: jvm支持了java的跨平台性; 一次编译,到处运行;

## 2. jre 和 jdk:
- jre: java运行时环境, 包含`JVM`和运行时所需要的`核心类库`;
- jdk: java的开发工具包, 包含`jre`和开发所需要的工具;

## 3.jdk的下载安装:
jdk15:[https://www.oracle.com/java/technologies/javase-jdk15-downloads.html](https://www.oracle.com/java/technologies/javase-jdk15-downloads.html)
jdk8:[https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
其他版本: [https://www.oracle.com/java/technologies/javase-downloads.html](https://www.oracle.com/java/technologies/javase-downloads.html)

## 4. 配置环境变量;
![](/images/javaLearn/001_1_环境变量.png)


# 三、HelloWorld入门:
## 1. 程序开发步骤说明:
java程序开发三步骤: **编写、编译、运行**
![](/images/javaLearn/001_2_step.png)
```java
/**
 * HelloWorld
 */
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello world");
    }
}
```
编译及运行:
![](/images/javaLearn/001_3_runHello.png)
**每次修改代码后都要重新`编译`**

## 2. java的注释:
Java中提供了三种注释方式，分别是：
- 单行注释: `//注释内容`
- 多行注释: `/*注释内容*/`
- 文档注释: `/**注释内容*/`

其中, 文档注释与多行注释作用基本相同,唯一的区别是文档注释可以使用`javadoc.exe`命令生成API文档;
# 四、变量：
## 1. 关键字:
　　关键字是被Java语言赋予特殊含义, 具有专门用途的单词, 比如之前接触的`class`, `public`, `static`, `void`均为Java已经预设好的; 你可以先把Java关键字理解为“命令”!
　　Java中关键字`均为小写`，下面是Java中所有关键字:

![](/images/javaLearn/001_4_关键字.png)

## 2. 标识符:
在Java程序中, 除了关键字以外基本上都是标识符了;
标识符就是名称的意思，所有的名称都统称为标识符; Java中经常要定义类、方法、变量, 在定义他们时总要给它们起名字, 这些名字就是标识符; 

标识符的规范:
- 组成元素:
    - 英文字符: `a-zA-Z`;
    - 数字: `0-9`;
    - 符号: `_与$`;
- 标识符规则:
    - 数字不能开头;
    - 不可以使用关键字;
    - 严格区分大小写，不限制长度起名时，尽量达到见名知意;

## 3. 常量:
- 字符串常量: 凡是用双引号引起来的部分;
- 整型常量: 直接写上的数字, 没有小数;
- 字符常量: 使用单引号引起来的单个字符;**单引号内不能没有数据**
- 布尔常量: 只有两种取值: `true`, `false`;
- 空常量: `null`: 表示没有任何数据; **null不能打印输出**

## 4. 数据类型:
### 4.1 分类:
- 基本数据类型;
- 引用数据类型:`字符串、类、接口、Lambda`；
### 4.2 基本数据类型：
**四类八种**
![](/images/javaLearn/001_5_数据类型.png)

**注意:**
- 字符串不是基本数据类型, 而是引用类型;
- 浮点型可能只是一个近似值, 并非精确值;
- 数据范围与字节数不一定相关,例如: `float`数据范围比`long`更加广泛, 但是`float`是4字节, `long`是8字节;
- 浮点数当中默认是`double`; 如果一定要使用`float`, 需要加后缀`F`(3.1415F);
- 整数当中默认是`int`;如果一定要使用`long`, 需要加后缀`L`(123456L);

## 5. 变量:
### 5.1 定义变量(创建变量):
定义变量的语法格式：
```java
数据类型  变量名  =  数据值；
int         a    =  100;
```
其中`int`是数据类型, 指定了变量只能存储整数, 而且指定了存储范围为`-2147483648～2147483648`;
其中`a`表示变量名, 变量名是标识符, 这说明只要是合法的标识符都可以用来做变量名;在程序中可以通过变量名来操作变量(内存中的小盒子);
其中`=100`是给变量赋值, 即向`a`变量中写入100(变量是个小盒子, 现在小盒子中保存的是100);
**注意**, 给变量赋的值一定要与类型符合, 也就是说`int`类型只能存储整数, 而且必须是在`-2147483648～2147483648`范围内的整数;

### 5.2 变量使用的注意事项
使用变量时, 要满足变量的使用规则才可以使用:

- 变量定义后可以不赋值, 使用时再赋值;不赋值不能使用;
```java
public static void main(String[] args) {
    int x;
    x = 20; //为x赋值20
    System.out.println(x);//读取x变量中的值，再打印
}
```
- 变量使用时有作用域的限制;
```java
public static void main(String[] args) {
    int x = 20;
    {
        int y = 20;
    }
    System.out.println(x);//读取x变量中的值，再打印
    System.out.println(y);//读取y变量中的值失败，失败原因，找不到y变量，因为超出了y变量作用范围，所以不能使用y变量
}
```
- 变量不可以重复定义;
```java
public static void main(String[] args){
    int x = 10;
    double x = 5.5;//编译失败，变量重复定义
}
```

## 6. 数据类型转换:
不同类型的变量是可以在一起运算的, 但要先进行类型转换再运算;
转换的过程中, 数据遵循一个原则:
- 范围小的数据类型值*如byte), 可以直接转换为范围大的数据类型值(如int);
- 范围大的数据类型值(如int), 不可以直接转换为范围小的数据类型值(如byte);

将各种数据类型按照数据范围从小到大依次列出：
`byte -> short -> int -> long -> float -> double`

### 6.1 自动类型转换
表示范围小的数据类型转换成范围大的数据类型, 这种方式称为*自动类型转换*;

自动类型转换格式:
`范围大的数据类型 变量 = 范围小的数据类型值;`
如：
```java
double d = 1000;
// 或
int i = 100;
double d2 = i;
```
### 6.2 强制类型转换
表示范围大的数据类型转换成范围小的数据类型, 这种方式称为*强制类型转换*;

强制类型转换格式:
`范围小的数据类型  变量 = (范围小的数据类型) 范围大的数据类型值;`
如：
```java
int  i = (int)6.718;   //i的值为6
// 或
double  d = 3.14;
int  i2 = (int)d;     //i2的值为3
```
**注意:**
- 强制类型转换,可能存在**数据溢出(整型)**或**精度损失(浮点)**的情况;
- `char`类型进行数学运算, 那么字符就会按照ASCII翻译成`int`;
```java
System.out.println(`A` + 1);    // 66
```
- `byte, short, char`在进行数学运算时都会被提升成`int`;

## 7.ASCII码表:
### 7.1 why:
数字和字符的对照关系表(编码表);
### 7.2 what:
- ASCII码表: 美国信息交换标准代码;
- Unicode码: 万国码, 开头0~127部分和ASCII完全一样, 后续部分包含更多字符;
- Emoji:表情也是一种对应的码表;

常见对应关系:
- 48 => '0';
- 65 => 'A';
- 97 => 'a';