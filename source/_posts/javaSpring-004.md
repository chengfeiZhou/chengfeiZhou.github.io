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
