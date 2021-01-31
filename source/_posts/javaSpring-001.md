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

### 1.5 Bean的依赖注入--对象:
#### a.一般方式注入:
方式: 在业务代码中获取dao
`spring/spring_ioc/src/main/java/com/itheima/service/UserService.java`
```java
package com.itheima.service;

public interface UserService {
    public void save();
}
```
`spring/spring_ioc/src/main/java/com/itheima/service/impl/UserServiceImpl.java`
```java
package com.itheima.service.impl;

import com.itheima.dao.UserDao;
import com.itheima.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserServiceImpl implements UserService {
    public void save() {
       // 从容器中获取Dao
       ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
       UserDao userDao = (UserDao) app.getBean("userDao");
       userDao.save();
   }
}
```
`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <!--  依赖注入  -->
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>
    <bean id="userService" class="com.itheima.service.impl.UserServiceImpl"></bean>
</beans>
```
`spring/spring_ioc/src/main/java/com/itheima/demo/UserController.java`
```java
// 业务代码:
package com.itheima.demo;


import com.itheima.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserController {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = (UserService) app.getBean("userService");
        userService.save();
    }
}
```
结果:
```shell
信息: ...
信息: ...
UserDaoImpl创建......
信息: ...
信息: ...
UserDaoImpl创建......
sace running......
```

#### b.依赖注入分析:
- 依赖注入: 它是spring框架核心IOC的具体实现;
- 在编写程序时,通过控制反转, 把对象创建交给spring, 但是代码中不可能出现没有依赖的情况;
- IOC解耦只是降低了他们的依赖关系, 但不会消除; 例如: 业务层仍会调用持久层的方法;
- 这种业务层和持久层的依赖关系, 在使用spring之后, 就让spring类维护了;
- 简单的说, 就是坐等框架把持久层对象传入业务层, 而不用自己来获取;

#### c.依赖注入的方式:
- **构造方法**;
- **set方法**;

#### d.使用set方法注入:
`spring/spring_ioc/src/main/java/com/itheima/service/UserService.java`
```java
package com.itheima.service;

public interface UserService {
    public void save();
}
```
`spring/spring_ioc/src/main/java/com/itheima/service/impl/UserServiceImpl.java`
```java
package com.itheima.service.impl;

import com.itheima.dao.UserDao;
import com.itheima.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserServiceImpl implements UserService {
    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
    public void save() {
        this.userDao.save();
    }

    //    public void save() {
//        // 从容器中获取Dao
//        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
//        UserDao userDao = (UserDao) app.getBean("userDao");
//        userDao.save();
//    }
}
```
`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <!--  依赖注入: set方法  -->
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>
    <bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
        <!-- 对象的引用用`ref`; 把"容器"内部的"userDao",通过"userService"的"setUserDao"方法注入给"userService"          -->
        <property name="userDao" ref="userDao"></property>
    </bean>
</beans>
```
`spring/spring_ioc/src/main/java/com/itheima/demo/UserController.java`
```java
// 业务代码:
package com.itheima.demo;

import com.itheima.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserController {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = (UserService) app.getBean("userService");
        userService.save();
    }
}
```
结果:
```shell
UserDaoImpl创建......
sace running......
```

如果业务代码**直接实例化**`UserDaoImpl`
`spring/spring_ioc/src/main/java/com/itheima/demo/UserController.java`
```java
package com.itheima.demo;


import com.itheima.service.UserService;
import com.itheima.service.impl.UserServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserController {
    public static void main(String[] args) {
//        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
//        UserService userService = (UserService) app.getBean("userService");
//        userService.save();
        UserService userService = new UserServiceImpl();
        userService.save();
    }
}

```
```shell
Exception in thread "main" java.lang.NullPointerException
	at com.itheima.service.impl.UserServiceImpl.save(UserServiceImpl.java:15)
	at com.itheima.demo.UserController.main(UserController.java:15)

Process finished with exit code 1
```
提示空指针异常, 原因是:
`userService`不是从spring容器中获取的, 没有执行`setUserDao`

#### e.set方法注入 -- p命名空间注入:
p命名空间注入本质也是set方法注入, 但比起上述的set方法注入更加方便, 主要体现在配置文件中, 如下:
- 需要引入P命名空间:
```xml
xmlns:p="http://www.springframework.org/schema/p"
```
- 需要修改注入方式:
```xml
<!-- 使用属性的方式: userDao-ref => 注入对象 -->
 <bean id="userService" class="com.itheima.service.impl.UserServiceImpl" p:userDao-ref="userDao"></bean>
```

#### f. 有参构造注入:
`spring/spring_ioc/src/main/java/com/itheima/service/impl/UserServiceImpl.java`
```java
package com.itheima.service.impl;

import com.itheima.dao.UserDao;
import com.itheima.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserServiceImpl implements UserService {
    private UserDao userDao;

    //    定义有参构造
    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }
    //    (必须)定义无参构造
    public UserServiceImpl() {
    }

    public void save() {
        this.userDao.save();
    }
}
```
`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <!--  构造方法:依赖注入  -->
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>
    <bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
         <!-- name: 有参构造参数名; ref: 容器中的id       -->
        <constructor-arg name="userDao" ref="userDao"></constructor-arg>
    </bean>
</beans>
```
### 1.6 Bean的依赖注入--其他数据类型:
除了对象的引用注入,**普通数据类型, 集合**等都可以在容器中进行注入;
注入数据三种数据类型:
- 引用数据注入: (上一节提到)
- 普通数据类型:
- 集合数据类型:

#### a.普通数据类型注入:
`spring/spring_ioc/src/main/java/com/itheima/dao/impl/UserDaoImpl.java`
```java
package com.itheima.dao.impl;
import com.itheima.dao.UserDao;

public class UserDaoImpl implements UserDao {
    private String username;
    private int age;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public UserDaoImpl() {
        System.out.println("UserDaoImpl创建......");
    }

    public void save() {
        System.out.println(username + "===============" + age);
        System.out.println("save running......");
    }
}

```
`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <!--  普通数据类型注入  -->
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl">
        <property name="username" value="zhangsan"></property>
        <property name="age" value="18"></property>
    </bean>
</beans>
```

业务代码:
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
结果:
```shell
UserDaoImpl创建......
zhangsan===============18
save running......
```


#### b.集合类型注入:
创建一个类,供后边引用:
`spring/spring_ioc/src/main/java/com/itheima/domain/User.java`
```java
package com.itheima.domain;

public class User {
    private String nama;
    private String addr;

    public String getNama() {
        return nama;
    }

    public void setNama(String nama) {
        this.nama = nama;
    }

    public String getAddr() {
        return addr;
    }

    public void setAddr(String addr) {
        this.addr = addr;
    }

    @Override
    public String toString() {
        return "User{" +
                "nama='" + nama + '\'' +
                ", addr='" + addr + '\'' +
                '}';
    }
}
```
集合数据类型注入:
`spring/spring_ioc/src/main/java/com/itheima/dao/impl/UserDaoImpl.java`
```java
package com.itheima.dao.impl;
import com.itheima.dao.UserDao;
import com.itheima.domain.User;

import java.util.List;
import java.util.Map;
import java.util.Properties;

public class UserDaoImpl implements UserDao {
    //    集合数据类型注入
    private List<String> strList;
    private Map<String, User> userMap;
    private Properties properties;

    public void setStrList(List<String> strList) {
        this.strList = strList;
    }

    public void setUserMap(Map<String, User> userMap) {
        this.userMap = userMap;
    }

    public void setProperties(Properties properties) {
        this.properties = properties;
    }
    public UserDaoImpl() {
        System.out.println("UserDaoImpl创建......");
    }

    public void save() {
        System.out.println(strList);
        System.out.println(userMap);
        System.out.println(properties);
        System.out.println("save running......");
    }
}
```
配置文件,配置集合注入:
`spring/spring_ioc/src/main/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>

    <!--  集合数据类型注入  -->
    <bean id="user1" class="com.itheima.domain.User">
        <property name="nama" value="张三"></property>
        <property name="addr" value="北京"></property>
    </bean>
    <bean id="user2" class="com.itheima.domain.User">
        <property name="nama" value="李四"></property>
        <property name="addr" value="上海"></property>
    </bean>
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl">
        <property name="strList">
            <list>
                <value>aaaaa</value>
                <value>bbbbb</value>
                <value>ccccc</value>
                <value>ddddd</value>
            </list>
        </property>
        <property name="userMap">
            <map>
            <!-- ref: 注入容器中的实例 -->
                <entry key="user1" value-ref="user1"></entry>
                <entry key="user2" value-ref="user2"></entry>
            </map>
        </property>
        <property name="properties">
            <props>
                <prop key="p1">ppp1</prop>
                <prop key="p2">ppp2</prop>
                <prop key="p3">ppp3</prop>
            </props>
        </property>
    </bean>
</beans>
```
业务代码:
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
结果:
```shell
UserDaoImpl创建......
[aaaaa, bbbbb, ccccc, ddddd]
{user1=User{nama='张三', addr='北京'}, user2=User{nama='李四', addr='上海'}}
{p1=ppp1, p2=ppp2, p3=ppp3}
save running......
```

## 2. 引入其他配置文件(分模块开发)
在实际开发中, Spring的配置内容非常多, 这就导致Spring配置很繁杂且体积很大, 所以, 可以将配置拆解到其他配置文件中, 而在Spring主配置文件中通过import标签进行加载:
```xml
<import resource="applicationContext-xxx.xml"/>
```

## 3. 总结:
Spring的重点配置:
- `<bean>`标签:
    - id属性: 在容器中Bean示例的唯一标识, 不允许重复;
    - class属性: 要实例化的Bean的全限定名;
    - scope属性: Bean的作用范围, 常用是`singleton`(默认)和`prototype`;
    - `<property>`标签: 属性注入:
        - name属性: 属性名称;
        - value属性: 注入普通属性值;
        - ref属性: 注入的对象引用
        - `<list>`标签
        - `<map>`标签
        - `<properties>`标签
        - ...
    - `<constructor-arg>` 标签
- `<import>`标签: 导入其他Spring配置分文件
