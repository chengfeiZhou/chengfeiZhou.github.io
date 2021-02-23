---
title: Java spring 入门三
date: 2021-02-21 14:15:49
tags:
    - Java
    - spring
categories:
    - Java
    - spring
---
# 一、Spring的AOP简介:
## 1. 什么是AOP:
AOP为Aspect Oriented Programming的缩写, 意为**面向切面编程**, 是用过过预编译方式和运行期动态代理实现程序功能统一维护的一种技术;
AOP是OOP的延续, 是如那件开发中的一个热点, 也是spring框架中的一个重要部分, 是函数式编程的一种衍生范型. 利用AOP可以对业务逻辑的各个部分进行隔离, 从而使得业务逻辑各个部分之间的耦合度降低, 提高程序的可重用性, 同时提高了开发的效率;

## 2. AOP的作用及其优势:
- 作用: 在程序运行期间, 在不修改源码的情况下对方法进行功能增强;
- 优势: 减少重复代码, 提高开发效率, 并且便于维护;
## 3.AOP的底层实现:
实际上, AOP的底层实现是通过Spring提供的动态代理技术实现的. 运行期间, Spring通过动态代理技术动态的生成代理对象, 代理对象方法执行时进行增强功能的接入, 再去调用目标对象的方法, 从而完成功能的增强;
## 4. AOP的动态代理技术:
常用的动态代理技术:
- JDK代理: 基于接口的动态代理技术;
- cglib代理: 基于父类的动态代理技术;

![](/images/javaSpringLearn/AopDongTai.png)

## 5. 基于JDK的动态代理:
- 新建一个java项目; `spring_aop/`
- 创建一个目标接口:
`spring_aop/src/main/java/proxy/jdk/TargetInterface.java`
```java
package proxy.jdk;

public interface TargetInterface {
    public void save();
}
```
- 创建目标对象:
`spring_aop/src/main/java/proxy/jdk/Target.java`
```java
package proxy.jdk;

public class Target implements TargetInterface {

    public void save() {
        System.out.println("save running...");
    }
}
```
- 创建功能增强对象:
`spring_aop/src/main/java/proxy/jdk/Advice.java`
```java
package proxy.jdk;

public class Target implements TargetInterface {

    public void save() {
        System.out.println("save running...");
    }
}
```
- 创建测试代码:
`spring_aop/src/main/java/proxy/jdk/ProxyTest.java`
```java
package proxy.jdk;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyTest {
    public static void main(String[] args) {
        // 创建目标对象
        final Target target = new Target();
        // 增强对象
        Advice advice = new Advice();
        // 返回值 就是动态生成的代理对象
        TargetInterface proxy = (TargetInterface) Proxy.newProxyInstance(
                target.getClass().getClassLoader(), // 目标对象类加载器
                target.getClass().getInterfaces(),  // 目标对象相同的接口字节码对象数组
                new InvocationHandler() {
                    // 调用代理对象的任何方法 实质执行的都是invoke方法
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        advice.beforeRun(); // 前置增强
                        method.invoke(target, args); // 执行目标方法
                        advice.afterRun(); // 后置增强

                        return null;
                    }
                }
        );
        // 调用代理对象方法
        proxy.save();
    }
}
```

## 6. cglib的动态代理:
cglib已集成到spring-core中
`spring_aop/src/main/java/proxy/cglib/ProxyTest.java`
```java
package proxy.cglib;

import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class ProxyTest {
    public static void main(String[] args) {
        // 创建目标对象
        final Target target = new Target();
        // 增强对象
        Advice advice = new Advice();
        // 返回值 就是动态生成的代理对象 基于cglib
        // 1. 创建增强器
        Enhancer enhancer = new Enhancer();
        // 2. 设置父类;
        enhancer.setSuperclass(Target.class);
        // 3. 设置回调:
        enhancer.steCallback(new MethodInterceptor() {
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                advice.beforeRun();     // 执行前置
                Object invoke = method.invoke(target, args);    // 执行目标
                advice.afterRun(); // 执行后置
                return invoke;
            }
        });

        // 4. 创建代理对象
        Target o = enhancer.create();

        // 5. 测试
        o.save();

    }
}
```
## 7. AOP相关概念:
Spring的AOP实现底层就是对上面的动态代理的代码进行了封装, 封装后只需要对需要关注的部分进行代码便携, 并通过配置的方式完成指定目标的方法增强;

常用术语:
- Target(目标对象): 代理的目标对象;
- Proxy(代理): 一个类被AOP织入增强后, 就产生一个结果代理类;
- Joinpoint(连接点): 是指那些被拦截到的点; 在spring中, 这些点指的是方法, 因为spring只支持方法类型的连接点 --- 可以被增强的方法叫做连接点;
- Pointcut(切入点): 指要对那些joinpoint进行拦截的定义;
- Advice(通知/增强): 指拦截到Joinpoint之后所要做的事情就是通知;
- Aspect(切面): 切点+通知的结合;
- Weaving(织入): 是指把增强应用到目标对象来创建新的代理对象的过程. Spring采用动态代理织入, 而AspectJ采用编译期织入和类装载期织入;

## 8.AOP开发明确事项:
### 8.1 需要编写的内容:
- 编写业务核心代码 (目标类的目标方法);
- 编写切面类, 切面类中有通知(增强功能方法);
- 在配置文件中, 配置织入关系, 即将哪些通知与哪些连接点进行结合;
### 8.2 AOP技术实现的内容:
Spring框架监控切入点方法的执行. 一旦监控到切入点方法被运行, 使用代理机制, 动态创建目标对象的代理对象, 根据通知类型, 在代理对象的对应位置, 将通知对应的功能织入, 完成完整代码逻辑运行;

### 8.3 AOP底层使用哪种代理方式:
在spring中, 框架会根据目标类是否实现了接口来决定采用哪种动态代理方式;

# 二、基于XML的AOP开发:
## 1. 快速入门:
- 导入AOP相关坐标;
- 创建目标接口和目标类(内部有切点);
- 创建切面类(内部有增强方法);
- 将目标类和切面类的对象创建权交给spring;
- 在applicationContext.xml中配置织入关系;
- 测试代码;

`spring_aop/pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ...>
    ...

    ...
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <!--  第三方  -->
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.4</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
    </dependencies>
</project>
```

定义目标接口
`spring_aop\src\main\java\aop\TargetInterface.java`
```java
package aop;

public interface TargetInterface {
    public void save();
}
```
定义目标类
`spring_aop\src\main\java\aop\Target.java`
```java
package aop;

public class Target implements TargetInterface{
    public void save() {
        System.out.println("savr running ....");
    }
}
```
定义增强类:
`spring_aop\src\main\java\aop\MyAspect.java`
```java
package aop;

public class MyAspect {
    public void before() {
        System.out.println("前置增强...");
    }
    public void afterRun() {
        System.out.println("后置增强...");
    }
}
```

注册bean
`spring_aop\src\main\resources\applicationContext.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--目标对象-->
    <bean id="target" class="aop.Target"></bean>
    <!--  切面对象  -->
    <bean id="myAspect" class="aop.MyAspect"></bean>

    <!--  配置织入: 高速spring框架, 那些方法(切点)需要那些增强 => 需要引入aop的命名空间  -->
    <aop:config>
        <!--  声明切面  -->
        <aop:aspect ref="myAspect">
            <aop:before method="before" pointcut="execution(public void aop.Target.save())"></aop:before>
            <aop:after-returnin method="afterRun" pointcut="execution(public void aop.Target.save())"></aop:after-returnin>
        </aop:aspect>
    </aop:config>
</beans>
```

测试代码:
`spring_aop\src\test\java\aop\AopTest.java`
```java
package aop;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class AopTest {
    @Autowired
    private TargetInterface target;

    @Test
    public void test1() {
        target.save();
    }
}
```

### 1.1 通知类型:
通知的配置语法:
`<aop:通知类型 method="切面类中方法名" pointcut="切点表达式"></aop:before>`

|名称|标签|说明|
|-|-|-|
|前置通知|<aop:before>|用于配置前置通知. 指定增强方法在切点方法前执行|
|后置通知|<aop:after-returning>|用于配置后置通知. 指定增强方法在切点方法之后执行|
|环绕通知|<aop:around>|用于配置环绕通知. 指定增强反方在切点方法之前和之后都执行|
|异常抛出通知|<aop:throwing>|用于配置异常抛出通知. 指定增强方法出现异常时执行|
|最终通知|<aop:after>|用于配置最终通知. 无论增强方法执行是否有异常都会执行|

`spring_aop\src\main\java\aop\MyAspect.java`
```java
package aop;

import org.aspectj.lang.ProceedingJoinPoint;

public class MyAspect {
    private int num = 0;

    public void before() {
        num ++;
        System.out.println("前置增强..." + num);
    }
    public void afterRun() {
        num += 10;
        System.out.println("后置增强..." + num);
    }

    public Object aroundRun(ProceedingJoinPoint pjp) throws Throwable { // ProceedingJoinPoint: 进程连接点 ==> 切点
        System.out.println("环绕前增强...");
        // 切点方法
        Object res = pjp.proceed();
        System.out.println("环绕后增强...");
        return res;
    }

    public void atferThrow() {
        System.out.println("异常增强...");
    }

    public void atferPower() {
        System.out.println("最终增强...");
    }
}
```

`spring_aop\src\main\resources\applicationContext.xml`
```java
<aop:config>
    <!--  声明切面  -->
    <aop:aspect ref="myAspect">
        <aop:before method="before" pointcut="execution(public void aop.Target.save())"></aop:before>
        <aop:after method="afterRun" pointcut="execution(public void aop.Target.save())"></aop:after>
        <aop:around method="aroundRun" pointcut="execution(* aop.*.*(..))"></aop:around>
        <aop:after-throwing method="atferThrow" pointcut="execution(* aop.*.*(..))"></aop:after-throwing>
        <aop:after method="atferPower" pointcut="execution(* aop.*.*(..))"></aop:after>
    </aop:aspect>
</aop:config>
```

执行结果:
```
前置增强...1
环绕前增强...
save running ....
最终增强...
环绕后增强...
后置增强...11
```
添加异常:
```
前置增强...1
环绕前增强...
最终增强...
异常增强...
后置增强...11
```


### 1.2 切点表达式:
`pointcut=`: 切点表达式; 用来指定`method`增强那些方法;
表达式语法:
`execution([修饰符]返回值类型 包名.类名.方法名(参数类型列表))`
 
- 访问修饰符可以不写;
- 返回值类型, 包名, 类名, 方法名可以使用*, 表示任意;
- 包名.类名 => 当前包下的类; 包名..类名 => 当前包及其子包下的类;
- 参数类型列表可以使用.. 表示任意个数, 任意类型的参数列表;

示例:
```
execution(public void com.xxx.aop.Target.method()) => com.xxx.aop包下Target类的返回值为void的公有(public)的method()方法;
execution(void com.xxx.aop.Targe.*(..)) => com.xxx.aop包下Target类的返回值为void的公有(public)的*所有*方法;
execution(* com.xxx.aop.*.*(..)) => (常用)com.xxx.aop包下的所有类的任意返回值的任意修饰符的*所有*方法;
execution(* com.xxx.aop..*.*(..)) => com.xxx.aop包*及其子包*下的所有类的任意返回值的任意修饰符的*所有*方法;
execution(* *..*.*(..)) => 所有
```

### 1.3 切点表达式抽取:
当多个增强的切点表达式相同时, 可以将切点表达式进行抽取, 在增强中使用pointcut-ref属性代替pointcut属性来引用抽取后的切点表达式.
```xml
<aop:config>
        <!--  声明切面  -->
        <aop:aspect ref="myAspect">
            <!-- 抽取切点表达式 -->
            <aop:pointcut id="myPointcut" expression="execution(public void aop.Target.save())"/>
            <aop:after method="atferPower" pointcut-ref="myPointcut"></aop:after>
        </aop:aspect>
    </aop:config>
```