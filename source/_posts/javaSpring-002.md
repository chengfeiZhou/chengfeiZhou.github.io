---
title: Java spring 入门二
date: 2021-01-31 18:31:46
tags:
    - Java
    - spring
categories:
    - Java
    - spring
---
# 一、Spring相关API:
## 1. ApplicationContext的继承体系:
`applicationContext`: 接口类型, 代表应用上下文, 可以通过其实例获得Spring容器中的Bean对象
![](/images/javaSpringLearn/002_applicationContext.png)

## 2. ApplicationContext的实现类:
### 2.1 ClassPathXMLApplicationContext:
它是从类的根路径下加载配置文件推荐使用这种

### 2.2 FileSystemXMLApplicationContext:
它是从磁盘路径上加载配置文件, 配置可以在磁盘的任意位置;

### 2.3 AnnotationConfigApplicationContext:
