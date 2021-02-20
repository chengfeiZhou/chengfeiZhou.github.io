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
当时用注解配置容器对象时, 需要使用此类来常见spring容器; 它用来读取注解;

## 3. getBean()方法使用:
```java
public Object getBean(String name) throw BeansException {
    // 容器中定义的id
    assertBeanFactoryActive();
    return getBeanFactory().getBean(name);
}

public <T> T getBean(class<T> requiredType) throws Beanxception {
    // requiredType: 字节码对象类型
    assertBeanFactoryActive();
    return getBeanFactory().getBean(requiredType);
}
```

# 二、Spring配置数据源:
## 1. 数据源(连接池)的作用:
- 数据源是提高程序性能出现的;
- 事先实例化数据源, 初始化部分连接资源;
- 使用连接资源时从数据源中获取;
- 使用完毕后将连接资源归还给数据源;

常见的数据源(连接池):**DBCP, C3P0, BoneCP, Druid等**
## 2.数据源手动创建:
- 创建一个新的maven项目:`spring_ioc_anno`
- 配置文件导入配置坐标:
`spring_ioc_anno/pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns=...>
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>spring_ioc_anno</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>14</maven.compiler.source>
        <maven.compiler.target>14</maven.compiler.target>
    </properties>
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.32</version>
        </dependency>
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```
- 创建一般测试代码:
`spring_ioc_anno/src/test/java/datasource/test/DataSourceTest.java`
```java
package datasource.test;

import com.alibaba.druid.pool.DruidDataSource;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;

import java.sql.Connection;


public class DataSourceTest {
    @Test
    public void test1() throws Exception {
        // 测试手动创建c3p0数据源
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
        dataSource.setUser("root");
        dataSource.setPassword("root123");
        Connection conn = dataSource.getConnection();

        // 使用数据库
        System.out.println(conn); // com.mchange.v2.c3p0.impl.NewProxyConnection@2928854b
        // 释方连接
        conn.close();
    }

    @Test
    public void test2() throws Exception {
        // 测试手动创建druid数据源
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/test");
        dataSource.setUsername("root");
        dataSource.setPassword("root123");
        Connection conn = dataSource.getConnection();
        // 使用数据库
        System.out.println(conn); // com.mysql.jdbc.JDBC4Connection@7334aada

        // 释方连接
        conn.close();
    }
}
```

不管是使用`c3p0`还是`druid`作为数据源, 数据源的配置与源码是耦合的; 因此将配置参数抽取到配置文件中;

- 创建配置文件:
`spring_ioc_anno/src/test/resources/jdbc.properties`
```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=root123
```
- 修改测试代码:
```java
package datasource.test;

import com.alibaba.druid.pool.DruidDataSource;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;

import java.sql.Connection;
import java.util.ResourceBundle;


public class DataSourceTest {
    @Test
    // 测试手动创建c3p0数据源(加载properties配置文件)
    public void test3() throws Exception {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        // 读取配置文件
        ResourceBundle rb = ResourceBundle.getBundle("jdbc");
        dataSource.setDriverClass(rb.getString("jdbc.driver"));
        dataSource.setJdbcUrl(rb.getString("jdbc.url"));
        dataSource.setUser(rb.getString("jdbc.username"));
        dataSource.setPassword(rb.getString("jdbc.password"));

        Connection conn = dataSource.getConnection();
        // 使用数据库
        System.out.println(conn); // com.mchange.v2.c3p0.impl.NewProxyConnection@5e0826e7
        // 释方连接
        conn.close();
    }

    @Test
    // 测试手动创建druid数据源(加载properties配置文件)
    public void test4() throws Exception {
        DruidDataSource dataSource = new DruidDataSource();
        ResourceBundle rb = ResourceBundle.getBundle("jdbc");
        dataSource.setDriverClassName(rb.getString("jdbc.driver"));
        dataSource.setUrl(rb.getString("jdbc.url"));
        dataSource.setUsername(rb.getString("jdbc.username"));
        dataSource.setPassword(rb.getString("jdbc.password"));

        Connection conn = dataSource.getConnection();
        // 使用数据库
        System.out.println(conn); // com.mysql.jdbc.JDBC4Connection@6e20b53a
        // 释方连接
        conn.close();
    }
}

```
## 3. spring配置数据源:
可以将DataSource的创建权交由Sprint容器去完成;
- 导入spring坐标:
`spring_ioc_anno/pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ...>
    <properties> ...</properties>
    <dependencies>
        ...
        <!-- 导入spring坐标-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
    </dependencies>
</project>
```
- 配置applicationContext.xml:
`spring_ioc_anno/src/test/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"></property>
        <property name="user" value="root"></property>
        <property name="password" value="root123"></property>
    </bean>
</beans>
```
- 测试代码:
`spring_ioc_anno/src/test/java/datasource/test/DataSourceTest.java`
```java
package datasource.test;

import com.alibaba.druid.pool.DruidDataSource;
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import javax.sql.DataSource;
import java.sql.Connection;
import java.util.ResourceBundle;


public class DataSourceTest {
    @Test
    // 测试spring容器产生数据源对象
    public void test5() throws Exception {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        DataSource dataSource = (DataSource) app.getBean("dataSource");
        Connection conn = dataSource.getConnection();
        System.out.println(conn);   // com.mchange.v2.c3p0.impl.NewProxyConnection@75c56eb9
        conn.close();
    }
}
```

## 4. 抽取jdbc配置文件:
`applicationContext.xml`加载`jdbc.properties`配置文件获得连接信息;
- 需要引入context命名空间和约束路径:
    - 命名空间: `xmlns:context="http://www.springframework.org/schema/context"`
    - 约束路径: `xsi:schemaLocation="
                http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
`
`spring_ioc_anno/src/test/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
                http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

<!-- 加载外部的properties文件 -->
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
    
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"></property>
        <property name="jdbcUrl" value="${jdbc.url}"></property>
        <property name="user" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>
</beans>
```

# 三、spring注解开发:
## 1. spring原始注解:
spring是轻代码重配置的框架, 配置比较繁重, 影响开发效率, 所以注解开发是一种趋势, 注解代替xml配置文件可以简化配置, 提高开发效率;
spring原始注解主要代替&lt;bean&gt;的配置:
|注解|说明|
|-|-|
|@Component|使用在类上用于实例化Bean|
|@Controller|使用在web层类上用于实例化Bean|
|@Service|使用在Service层类上用于实例化Bean|
|@Repository|使用在dao层类上用于实例化Bean|
|@Autowired|使用在字段上用于根据类型依赖注入|
|@Qualifier|结合@Autowired一起使用用于根据名称进行依赖注入|
|@Resource|相当于@Autowired+@Qualifier, 按照名称进行注入|
|@Value|注入普通属性|
|@Scope|标注Bean的作用范围|
|@PostConstruct|使用在方法上标注该方法是Bean的初始化方法|
|@PreDestroy|使用在方法上标注该方法是Bean的销毁方法|

**注意:**
使用注解进行开发时,需要在applicationContext.xml中配置组件扫描, 作用是 指定哪个包及其子包下的Bean需要进行扫描以便识别使用注解配置的类,字段和方法
```xml
<!--  注解的组件扫描  -->
<context:component-scan base-package="datasource"></context:component-scan>
```

## 2. 搭建初始测试环境:
- 构建dao层:
`spring_ioc_anno/src/test/java/datasource/dao/UserDao.java`
```java
package datasource.dao;

public interface UserDao {
    public void save();
}
```
`spring_ioc_anno/src/test/java/datasource/dao/impl/UserDaoImpl.java`
```java
package datasource.dao.impl;

import datasource.dao.UserDao;

public class UserDaoImpl implements UserDao {
    public void save() {
        System.out.println("save running....");
    }
}
```
- 构建Service层:
`spring_ioc_anno/src/test/java/datasource/service/UserService.java`
```java
package datasource.service;

public interface UserService {
    public void save();
}
```
`spring_ioc_anno/src/test/java/datasource/service/impl/UserServiceImpl.java`
```java
package datasource.service.impl;

import datasource.dao.UserDao;
import datasource.service.UserService;

public class UserServiceImpl implements UserService {
    private UserDao userDao;
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void save() {
        userDao.save();
    }
}
```
- 构建web层:
`spring_ioc_anno/src/test/java/datasource/web/UserController.java`
```java
package datasource.web;

import datasource.service.UserService;
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
- 使用xml文件配置
`spring_ioc_anno/src/test/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
    <bean id="userDao" class="datasource.dao.impl.UserDaoImpl"></bean>
    <bean id="userService" class="datasource.service.impl.UserServiceImpl">
        <property name="userDao" ref="userDao"></property>
    </bean>
</beans>
```

## 3. 使用注解配置Bean:
`spring_ioc_anno/src/test/java/datasource/dao/impl/UserDaoImpl.java`
```java
package datasource.dao.impl;

import datasource.dao.UserDao;
import org.springframework.stereotype.Component;

//<bean id="userDao" class="datasource.dao.impl.UserDaoImpl"></bean>
//@Component("userDao")
@Repository("userDao")
public class UserDaoImpl implements UserDao {
    public void save() {
        System.out.println("save running....");
    }
}
```
`spring_ioc_anno/src/test/java/datasource/service/impl/UserServiceImpl.java`
```java
package datasource.service.impl;

import datasource.dao.UserDao;
import datasource.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.ImportResource;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;

//<bean id="userService" class="datasource.service.impl.UserServiceImpl">
//@Component("userService")
@Service("userService")
public class UserServiceImpl implements UserService {
//    <property name="userDao" ref="userDao"></property>    // 注入
    @Autowired  // 按照数据类型产品能够spring容器中进行匹配
//    @Qualifier("userDao")   // 按照名称注入: 可按照id名称从容器中进行匹配, 但是此处 @Qualifier需要结合 @Autowired 使用
//    @Resource(name="userDao")     // 相当于@Autowired+@Qualifier, 按照名称进行注入
    private UserDao userDao;
    // 如果使用xml的方式注入, set方法不能省略; 如果使用注解的方式, 则可以省略
//    public void setUserDao(UserDao userDao) {
//        this.userDao = userDao;
//    }

    @Value("${jdbc.driver}")    // 使用@Value进行参数注入
    private String driver;


    public void save() {
        System.out.println(driver); // com.mysql.jdbc.Driver
        userDao.save();
    }

    @PostConstruct
    public void init() {
        System.out.println("Service对象的初始化方法");
    }

    @PreDestroy
    public void destory() {
        System.out.println("Service对象的销毁方法");
    }
}

```
删除`applicationContext.xml`中关于以上Bean的配置, 并添加组件扫描;


## 4.Spring的新注解:
使用上边的注解不能全部代替xml配置文件, 还需要使用注解替代的配置如下:
- 非自定义的Bean的配置: &lt;bean&gt;
- 加载properties文件的配置: &lt;context:property-placeholder&gt;
- 组件扫描的配置: &lt;context:component&gt;
- 引入其他文件: &lt;import&gt;


新注解:

|注解|说明|
|-|-|
|@Configuration|用于指定当前类是一个spring配置类, 当创建容器时会从类上加载注解|
|@ComponentScan|用于指定Spring在初始化容器时要扫描的包;
|@Bean|使用在方法上, 标注将该方法的返回值存储到spring容器中|
|@PropertySource|用于加载.peoperties文件中的配置|
|@Import|用于导入其他配置类|

## 5. 使用新注解:
- 创建config包,用于存放配置类:
`spring_ioc_anno/src/test/java/datasource/config/DataSourceConfiguration.java`
用于配置数据源
```java
package datasource.config;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

//<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>: 加载外部的properties文件
@PropertySource("classpath:jdbc.properties")
public class DataSourceConfiguration {
    @Value("jdbc.driver")
    private String driver;

    @Value("jdbc.url")
    private String url;

    @Value("jdbc.username")
    private String username;
    @Value("jdbc.password")
    private String password;

    @Bean("dataSource") // Spring会将当前方法的返回值以指定名称存储到spring容器中
    public DataSource getDataSource() throws Exception {
        // 测试手动创建c3p0数据源
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(driver);
        dataSource.setJdbcUrl(url);
        dataSource.setUser(username);
        dataSource.setPassword(password);
        return dataSource;
    }
}
```
`spring_ioc_anno/src/test/java/datasource/config/SpringConfiguration.java`
spring的主配置类
```java
package datasource.config;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.*;

import javax.sql.DataSource;
import java.beans.PropertyVetoException;
import java.sql.Connection;
import java.util.ResourceBundle;

// 标志该类是Spring的核心配置
@Configuration
//<context:component-scan base-package="datasource"></context:component-scan> : 组件扫描
@ComponentScan("datasource")
// <import resource="/"/>
@Import({DataSourceConfiguration.class})    // 这是一个数组, 可以添加多个
public class SpringConfiguration { }
```
- 修改getBean时, 配置的加载方法:
`spring_ioc_anno/src/test/java/datasource/web/UserController.java`
```java
package datasource.web;

import datasource.config.SpringConfiguration;
import datasource.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class UserController {
    public static void main(String[] args) {
//        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        ApplicationContext app = new AnnotationConfigApplicationContext(SpringConfiguration.class);
        UserService userService = (UserService) app.getBean("userService");
        userService.save();
    }
}
```

# 四、Spring整合Junit:
## 1. 原始Junit测试Spring的问题:
在测试类中, 每个测试方法都有以下两行代码:
```java
ApplicationContext app = new ClassPathXmlApplicationContext("base.xml");
IAccountService as = app.getBean("accountService", IAccountService.class);
```
这两行代码的作用是获取容器, 如果不写, 直接会提示空指针异常; 所以不会轻易删掉;

## 2. 上述问题解决思路:
- 让springJunit负责创建Spring容器, 但是需要将配置文件的名称告诉容器;
- 将需要进行测试的Bean直接在测试类中进行注入;

## 3. Spring继承Junit步骤:
- 导入spring继承Junit的坐标;
- 使用@Runwith注解替换原来的运行期;
- 使用@ContextConfiguration指定配置文件或配置类;
- 使用@Autowired注入需要测试的对象;
- 创建测试方法进行测试;

`spring_ioc_anno/pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ...>
    ...
    <properties>
        ...
    </properties>
    <dependencies>
        ...
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.1</version>
            <scope>test</scope>
        </dependency>
        <!-- 导入spring坐标-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
    </dependencies>
</project>
```
`spring_ioc_anno/src/test/java/datasource/test/SprintJunitTest.java`
```java
package datasource.test;

import datasource.config.SpringConfiguration;
import datasource.service.UserService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.sql.DataSource;
import java.sql.SQLException;

@RunWith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration("classpath:applicationContext.xml")
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SprintJunitTest {
    @Autowired
    private UserService userService;

    @Autowired
    private DataSource dataSource;

    @Test
    public void test1() {
        userService.save();
    }

    @Test
    public void test2() throws SQLException {
        System.out.println(dataSource.getConnection());
    }
}

```