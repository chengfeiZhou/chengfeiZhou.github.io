---
title: python标准库-时间类型
date: 2021-03-02 11:54:09
tags: 
    - python
    - 标准库
    - 时间类型
categories: 
    - python
    - 标准库
    - 时间类型
---
# 一、datetime:处理日期和时间的类:
## 1. 概述:
源码:[https://github.com/python/cpython/tree/3.9/Lib/datetime.py](https://github.com/python/cpython/tree/3.9/Lib/datetime.py)

在支持日期时间数学运算的同时，实现的关注点更着重于如何能够更有效地解析其属性用于格式化输出和数据操作;
## 2. 感知型对象和简单型对象:
感知型对象就能相对于其他感知型对象来精确定位自身时间点。 感知型对象是用来表示一个没有解释空间的固定时间点;
简单型 对象没有包含足够多的信息来无歧义地相对于其他 date/time 对象来定位自身时间点;
对于要求感知型对象的应用，`datetime` 和 `time` 对象具有一个可选的时区信息属性 `tzinfo`，它可被设为抽象类 `tzinfo` 的子类的一个实例。 这些 `tzinfo` 对象会捕获与 UTC 时间的差值、时区名称以及夏令时是否生效等信息;

## 3. 常量:
`datetime.MINYEAR`: `date` 或者 `datetime` 对象允许的最小年份。 常量 `MINYEAR` 是 1 ;
`datetime.MAXYEAR`: `date` 或 `datetime` 对象允许最大的年份。常量 `MAXYEAR` 是 `9999`;

