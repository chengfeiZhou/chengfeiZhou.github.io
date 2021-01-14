---
title: Java spring 入门一
date: 2021-01-13 15:54:23
tags:
    - Java
    - spring
categories:
    - Java
    - spring
---
# 一、spring介绍:
## 1. spring是什么:
Spring是分层的Java SE/EE应用full-stack轻量级开源框架, 以`IoC`(反转控制)和`AOP`(面向切面编程)为内核;
提供**展现层SpringMVC**和**持久层Spring JDBC Template**以及**业务层事务管理**等众多的企业级应用技术, 还能整合开源世界众多著名的第三方框架爱和类库;
## 2. spring发展;
...

## 3.spring优势:
- **方便解耦, 简化开发**:通过Spring提供的IoC容器, 可以将对象间的依赖交由Spring进行控制,避免硬编码所造成的的过渡耦合;用户不必再为单例模式类、属性文件解析等底层需求写代码, 而更专注于上层应用;
- **AOP编程的支持**: 许多不易用传统OOP实现的功能可以通过AOP轻松实现;
- **声明式事务支持**: 通过配置的方式, 解放事务管理代码;
- **方便程序测试**:
- **方便集成各种优秀的框架**:
- **降低了JavaEE API的使用难度**: spring对javaEE进行了薄封装, 使API使用难度降低;
- **java源码典范**: spring设计的妙啊;

## 4. spring的体系结构:
![](/images/javaSpringLearn/001_SpringFramework.png)

# 二、Spring快速入门:
## 1. spring程序开发步骤:
- 导入spring的maven坐标;
- 编写DAO(如: userDaolmpl)接口和实现类;
- 创建spring核心xml配置文件;
- 在xml配置文件中配置userDaolmpl;
- 通过spring的API获取Bean实例;

## 2. 代码演示:
- 导入spring:
`/spring/spring_ioc/pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.zhouchengfei</groupId>
    <artifactId>spring_ioc</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>14</maven.compiler.source>
        <maven.compiler.target>14</maven.compiler.target>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
    </dependencies>

</project>
```
- 编写DAO(如: userDaolmpl)接口和实现类:
`spring/spring_ioc/src/main/java/com/itheima/dao/UserDao.java`
```java
package com.itheima.dao;

public interface UserDao {
    public void save();
}
```
`spring/spring_ioc/src/main/java/com/itheima/dao/impl/UserDao.java`
```java
package com.itheima.dao.impl;
import com.itheima.dao.UserDao

public class UserDao implements UserDao {
    public void save() {
        System.out.println("sace running......");
    }
}
```
- 创建spring核心xml配置文件;
`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- add bean -->
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>
</beans>
```
- 通过spring的API获取Bean实例:
`spring/spring_ioc/src/main/java/com/itheima/demo/UserDaoDemo.java`
```java
package com.itheima.demo;

import com.itheima.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserDaoDemo {
    public static void main(String[] args) {
        // 通过spring的API获取Bean实例
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) app.getBean("userDao");
        userDao.save();
    }
}
```

# 三、spring配置文件:
`spring/spring_ioc/src/main/resources/applicationContext.xml`
## 1. Bean标签:
### 1.1 基本配置:
- id: 唯一标识;
- class: 全限定名; 必须存在"无参构造函数";
### 1.2 范围配置:
scope: 指对象的作用范围, 取值如下:
|取值|说明|
|-|-|
|**singleton**|(默认值)单例的|
|**prototype**|多例的|
|request|Web项目中, Spring创建一个Bean对象, 将对象存入到request域中|
|session|Web项目中, Spring创建一个Bean对象, 将对象存入到session域中|
|global session|Web项目中, 应用在Portlet环境, 如果没有Portlet环境, `global session`相当于session|

####  使用`junit`进行测试`scope`属性:
##### a. `singleton`与`prototype`:
- 注册`junit`:

`pring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ...>
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.zhouchengfei</groupId>
    <artifactId>spring_ioc</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        ...
    </properties>
    <dependencies>
       ...

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>
    </dependencies>
</project>
```
- 单元测试代码:
`spring/spring_ioc/src/test/java/com/itheima/test/SpringTest.java`
```java
package com.itheima.test;
import org.junit.Test;

import com.itheima.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class SpringTest {
    @Test
    // 测试scope属性
    public void test1() {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao1 = (UserDao) app.getBean("userDao");
        UserDao userDao2 = (UserDao) app.getBean("userDao");
        System.out.println(userDao1);
        System.out.println(userDao2);
    }
}
```
##### b. 实例创建时机不同:
`spring/spring_ioc/src/main/java/com/itheima/dao/impl/UserDaoImpl.java`
```java
package com.itheima.dao.impl;
import com.itheima.dao.UserDao;

public class UserDaoImpl implements UserDao {
    public UserDaoImpl() {
        // 增加一个无参构造方法
        System.out.println("UserDaoImpl创建......");
    }

    public void save() {
        System.out.println("sace running......");
    }
}
```
测试结果:
- scope="singleton":
```shell
UserDaoImpl创建......
com.itheima.dao.impl.UserDaoImpl@1fe20588
com.itheima.dao.impl.UserDaoImpl@1fe20588
```
**注意:**
对象的创建是在加载配置文件的时就创建了;

- scope="prototype":
```shell
UserDaoImpl创建......
UserDaoImpl创建......
com.itheima.dao.impl.UserDaoImpl@14d3bc22
com.itheima.dao.impl.UserDaoImpl@12d4bf7e
```
**注意:**
对象的创建是在加载配置文件的时没有创建, 而是在`getBean`的时候创建;

##### c.结论:
||singleton|prototype|
|-|-|-|
|Bean的实例化个数|1个|多个|
|Bean的实例化时机|当Spring核心文件被加载时, 实例化配置的Bean示例|当调用getBean()时实例化Bean|
|Bean的生命周期|1. 对象创建: 当应用加载, 创建容器时, 对象就被创建了;<br/>2. 对象运行: 只要容器在, 对象一直活着;<br/>3. 对象销毁: 当应用卸载, 销毁容器时, 对象就被销毁;|1. 对象创建: 当时用对象时, 创建新的对象示例;<br/>对象运行: 只要对象在使用, 就一直活着;<br/>3. 对象销毁: 当对象长时间不使用, 被java GC;


### 1.3 生命周期配置:
- init-method: 指定类中的初始化方法;
- destroy-method: 指定类中销毁方法名称;

`spring/spring_ioc/src/main/java/com/itheima/dao/impl/UserDaoImpl.java`
```java
package com.itheima.dao.impl;
import com.itheima.dao.UserDao;

public class UserDaoImpl implements UserDao {
    public UserDaoImpl() {
        System.out.println("UserDaoImpl创建......");
    }

    public void init() {
        System.out.println("这是初始化方法...");
    }
    public void destroy() {
        System.out.println("这是销毁方法...");
    }

    public void save() {
        System.out.println("sace running......");
    }
}
```
`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl" init-method="init" destroy-method="destroy"></bean>
</beans>
```

`spring/spring_ioc/src/main/resources/applicationContext.xml`
```java
package com.itheima.test;
import org.junit.Test;

import com.itheima.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class SpringTest {
    @Test
    // 测试生命周期
    public void test2() {
        ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao1 = (UserDao) app.getBean("userDao");
        System.out.println(userDao1);
        app.close(); // 手动关闭容器;
        // ApplicationContext: 没有关闭方法, 但是其子类ClassPathXmlApplicationContext 有关闭方法
    }
}

```

结果:
```
...
信息: ...
信息: ...
UserDaoImpl创建......
这是初始化方法...
com.itheima.dao.impl.UserDaoImpl@61dd025
1月 14, 2021 6:41:51 下午 org.springframework.context.support.AbstractApplicationContext doClose
信息: ...
这是销毁方法...
```
### 1.4 Bean实例化配置:
- **无参构造方法**实例化;
- **工厂静态方法**实例化;
- **工厂实例方法**实例化; 

#### a.工厂静态方法:
`spring/spring_ioc/src/main/java/com/itheima/factory/StaticFactory.java`
```java
package com.itheima.factory;

import com.itheima.dao.UserDao;
import com.itheima.dao.impl.UserDaoImpl;

public class StaticFactory {
    public static UserDao getUserDao() {
        return new UserDaoImpl();
    }
}
```

`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
<!--    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl" init-method="init" destroy-method="destroy"></bean>-->
    <bean id="userDao" class="com.itheima.factory.StaticFactory" factory-method="getUserDao"></bean>
</beans>
```

`spring/spring_ioc/src/test/java/com/itheima/test/SpringTest.java`
```java
package com.itheima.test;
import org.junit.Test;

import com.itheima.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class SpringTest {
    @Test
    // 测试静态工厂构造
    public void test2() {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao1 = (UserDao) app.getBean("userDao");
        System.out.println(userDao1);
    }
}
```

结果:
```shell
UserDaoImpl创建......
com.itheima.dao.impl.UserDaoImpl@1a04f701
```

#### b.工厂实例方法:
`spring/spring_ioc/src/main/java/com/itheima/factory/DynamicFactory.java`
```java
package com.itheima.factory;

import com.itheima.dao.UserDao;
import com.itheima.dao.impl.UserDaoImpl;

public class DynamicFactory {
    public UserDao getUserDao() {
        return new UserDaoImpl();
    }
}
```
`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
<!--    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl" init-method="init" destroy-method="destroy"></bean>-->
    <!--  工厂静态构造    -->
<!--    <bean id="userDao" class="com.itheima.factory.StaticFactory" factory-method="getUserDao"></bean>-->
    <!--  工厂实例构造  -->
    <bean id="factory" class="com.itheima.factory.DynamicFactory"></bean>
    <bean id="userDao" factory-bean="factory" factory-method="getUserDao"></bean>
</beans>
```