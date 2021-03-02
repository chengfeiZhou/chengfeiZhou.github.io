---
title: 四、jdbcTemplate基本使用、Spring实物控制
date: 2021-02-23 23:12:23
tags:
    - Java
    - spring
categories:
    - Java
    - spring
---
# 一、Spring JdbcTemplate基本使用:
## 1. JdbcTemplate概述:
它是spring框架中提供的一个对象, 是对原始繁琐的Jdbc API对象的简单封装. Spring框架提供了很多的操作模板类. 
例如: 操作关系型数据的JdbcTemplate和HibernateTemplate, 操作nosql数据库的RedisTemplate, 操作消息队列的JMSTemplate等等;

## 2. JdbcTemplate开发步骤:
- 导入spring-jdbc和spring-tx坐标;
- 创建数据库表和实体;
- 创建JdbcTemplate对象;
- 执行数据库操作;

`spring_jdbc/pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ...>
    ...
    <properties>
        ...
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
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
    </dependencies>
</project>
```
- 创建实体对象;
`spring_jdbc/src/test/java/domain/Account.java`
```java
package domain;

public class Account {
    private String name;
    private double money;

    public String getName() {
        return name;
    }

    public double getMoney() {
        return money;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setMoney(double money) {
        this.money = money;
    }

    @Override
    public String toString() {
        return "Account{" +
                "name='" + name + '\'' +
                ", money=" + money +
                '}';
    }
}
```
- 业务代码:
`spring_jdbc/src/test/java/test/JdbcTemplateTest.java`
```java
package test;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.junit.Test;
import org.springframework.jdbc.core.JdbcTemplate;


public class JdbcTemplateTest {
    @Test
    // 测试JdbcTempl
    public void test1() throws Exception {
        // 创建数据源对象
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://127.0.0.1:3306/test");
        dataSource.setUser("root");
        dataSource.setPassword("root123");

        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        // 设置数据源对象
        jdbcTemplate.setDataSource(dataSource);
        // 执行操作
        int row = jdbcTemplate.update("insert into account values(?, ?)", "tom", 5000);
        System.out.println(row);    // 1
    }
}
```

## 3. Spring产生JdbcTemplate对象:
将JdbcTemplate的创建权交给Spring, 将数据源DataSource的创建权也交给Spring, 在Spring容器内部将数据源Database注入到JdbcTemplate模板对象中, 配置如下:
`spring_jdbc/src/test/resources/applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/test"></property>
        <property name="user" value="root"></property>
        <property name="password" value="root123"></property>
    </bean>
    <!--  jdbcTemplate-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
</beans>
```

测试代码:
`spring_jdbc/src/test/java/test/JdbcTemplateTest.java`
```java
package test;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;

import javax.swing.*;


public class JdbcTemplateTest {
    // 测试Spring获取JdbcTemplate
    @Test
    public void test2() {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        JdbcTemplate jdbcTemplate = app.getBean(JdbcTemplate.class);
        // 执行操作
        int row = jdbcTemplate.update("insert into account values(?, ?)", "jack", 1000);
        System.out.println(row);    // 1
    }
}
```
## 4. 抽取数据库配置参数:
参考: 二、Spring相关配置, 数据源及注解开发/二、Spring配置数据源

## 5. JdbcTemplate的常用操作:
```java
package test;

import domain.Account;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class JdbcTemplateCRUD {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Test
    public void testInsert() {
        int row = jdbcTemplate.update("insert into account values(?, ?)", "zhangsan", 1000);
        System.out.println(row);
    }

    @Test
    public void testUpdate() {
        int row = jdbcTemplate.update("update account set money=? where name=?", 10000, "tom");
        System.out.println(row);
    }

    @Test
    public void testDelete() {
        int row = jdbcTemplate.update("delete from account where name=?", "tom");
        System.out.println(row);
    }

    @Test
    public void testQueryAll() {
        List<Account> accountList =  jdbcTemplate.query("select * from account", new BeanPropertyRowMapper<Account>(Account.class));
        System.out.println(accountList);
    }

    @Test
    public void testQueryOne() {
        Account account =  jdbcTemplate.queryForObject("select * from account where name=?",new BeanPropertyRowMapper<Account>(Account.class), "jack");
        System.out.println(account);    // Account{name='jack', money=50000.0}
    }

    @Test
    public void testQueryCount() {
        // 聚合查询
        Long count =  jdbcTemplate.queryForObject("select count(*) from account", Long.class);
        System.out.println(count);  // 4
    }

}
```

# 二、Spring的事务控制:

## 1.编程式事务控制相关对象:
### 1.1 PlatformTransactionManager: 平台事务管理器
PlatformTransactionManager接口是Spring的事务管理器, 它里面提供了常用的操作事务的方法:
|方法|说明|
|-|-|
|TransactionStatus getTransaction(TransactionDefination defination)|获取事务的状态信息|
|void commit(TransactioStatus status)|提交事务|
|void rollback(TransactionStatus status)|回滚事务|

**注意:**
> PlatformTransactioManager是接口类型, 不同的Dao层技术则有不同的实现类;
> 例如:
> - Dao层技术是jdbc或mybatis时, `org.springframework.jdbc.datasource.DataSourceTransactionManager`;
> - Dao层技术是hibernta时: `org.springframework.orm.hibernate5.HibernateTransactionManager;`

### 1.2 TransactionDefinition:
TransactionDefinition是事务的定义信息对象, 里面有如下方法:
|方法|说明|
|-|-|
|int getIsolationLevel()|获取事务的隔离级别|
|int getPropogationBehavior()|获取事务的传播行为|
|int getTimeout()|获取超时时间|
|boolean isReadOnly()|是否只读|

#### 1.2.1 事务的隔离级别:
设置隔离级别, 可以解决事务并发产生的问题, 如: 脏读, 不可重复读, 虚读(幻读)
- ISOLATION_DEFAULT: 默认
- ISOLATION_READ_UNCOMMITTED: 读未提交(不能解决)
- ISOLATION_READ_COMMITTED: 读已提交(解决*脏读*)
- ISOLATION_REPEATABLE_READ: 可重复读(解决*不可重复读*)
- ISOLATION_SERIALIZABLE: 串行化(全能解决, 但性能低, 相当于锁表)

#### 1.2.2 事务的传播行为:
作用: 解决业务方法在调用业务方法时, 方法之间事务统一性的问题

- **REQUIRED**: 如果当前没有事务, 就新建一个事务, 如果已经存在一个事务中, 加入到这个事务中; 一般的选择(默认值);
- **SUPPORTS**: 支持当前事务, 如果当前没有事务, 就以非事务方式执行(没有事务);
- MANDATORY: 使用当前事务, 如果没有事务,就抛出异常;
- REQUERS_NEW: 新建事务, 如果当前在事务中, 就把当前事务挂起;
- NOT_SUPPORTED: 以非事务方式执行操作, 如果当前存在事务, 就把当前事务挂起;
- NEVER: 以非事务方式执行操作, 如果当前存在事务, 抛出异常;
- NESTED: 如果当前存在事务, 则在嵌套事务内执行; 如果当前没有事务, 则执行REQUIRED类似的操作;
- 超时时间: 默认值是-1, 没有超时限制; 如果有, 以秒为单位进行设置;
- 是否只读: 建议查询设置为只读;

## 1.3 TransactionStatus: 事务状态对象:
TransactionStatus接口提供的是事务具体的运行状态, 方法介绍如下:
|方法|说明|
|-|-|
|boolean hasSavepoint()|是否存储回滚点|
|boolean isCompleted()|事务是否完成|
|boolean isNewTransaction()|是否是新事物|
|boolean isRollbackOnly()|事务是否回滚|

# 2. 基于Xml的声明式事务控制:
### 2.1 什么是声明式事务控制:
Spring的声明式事务就是**采用声明的方式来处理事务**
#### 声明式事务处理的作用:
- 事务管理不侵入开发的组件; 
- 在不需要事务的时候, 只要在配置文件上修改一下, 即可移去事务管服务;

**Spring声明式事务控制底层就是AOP**
