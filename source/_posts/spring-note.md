---
title: Spring 学习笔记
date: 2017-10-27 09:00:00
tags: 
    - Spring
    - Java
    - IoC
    - 依赖注入
categories: 学习笔记
reward: true
toc: true
---


## 1. Spring 是什么？
Spring是一个分层的 JavaSE/EE **full-stack(一站式)** 轻量级开源框架。
## 2. Spring IoC
### 1. IoC 是什么
Ioc 全称是 Inversion of Control 即“控制反转”，一种设计思想。

Ioc就是由容器来负责控制对象的生命周期和对象间的关系。IoC对编程带来的最大改变不是从代码上，而是从思想上，发生了“主从换位”的变化。应用程序原本是老大，要获取什么资源都是主动出击，但是在IoC/DI思想中，应用程序就变成被动的了，被动的等待IoC容器来创建并注入它所需要的资源了。

IoC很好的体现了面向对象设计法则之一————好莱坞法则：“别找我们，我们找你”；即由IoC容器帮对象找相应的依赖对象并注入，而不是由对象主动去找。

<!-- more -->

### 2. DI 是什么
DI 全称 Dependency Injection，即“依赖注入”，一种思想。

DI就是由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。

### 3. IoC 和 DI 的关系
IoC和DI由什么关系呢？其实DI是Ioc具体实现的其中一种方式。

由于控制反转概念比较含糊（可能只是理解为容器控制对象这一个层面，很难让人想到谁来维护对象关系），所以2004年大师级人物Martin Fowler又给出了一个新的概念：“依赖注入”，相对IoC 而言，“依赖注入”明确描述了“被注入对象依赖IoC容器配置依赖对象”。

### 4. IoC 容器
IoC 容器是 Spring 框架的核心。容器可以解析XML配置文件，通过java反射机制将对象创建出来，设置它们的依赖关系，并管理它们的整个生命周期从对象创建到销毁。

Application Context 是 spring 中较高级的容器。它可以加载配置文件中定义的 bean，将所有的 bean 集中在一起，当有请求的时候分配 bean。

常用的 ApplicationContext 接口实现类：

- `FileSystemXmlApplicationContext`：从本地文件系统中加载XML文件，使用该实现类时需要指定配置文件在磁盘中的绝对路径。

- `ClassPathXmlApplicationContext`：从 CLASSPATH 环境变量加载XML文件，需指定XML文件的文件名，该实现类会从CLASSPATH中搜索，找到指定的文件。

- `WebXmlApplicationContext`：在web应用中使用，该实现类可以加载Servlet响应的XML文件。

## 2. Spring bean
### 1. Spring bean是什么
bean 是Spring IoC容器所管理的对象，在容器中被实例化，组装。bean 由XML文件的配置元数据创建。
### 2. bean 的xml配置
bean的基本语法：
```xml
<bean id="对象引用" class="完整类名" scope="对象作用域"></bean>
```
bean有以下常用属性：

1. id 和 name

    都可以用来指定对象的引用名称，id和name 可以同时存在，同时存在时id的值不能为空。id或name必须指定其中的一个。

2. class

    这个属性是必须的，用来指定创建的对象的完整类名。

3. scope

    指定对象的作用域。有`singleton`，`prototype`，`request`，`session` 这四个值，默认值是`singleton`。

4. factory-method

    指定使用静态工厂方式实例化对象时调用的静态工厂方法

5. factory-bean

    指定使用实例工厂方式实例化对象时使用的实例工厂类

6. autowiring

    指定bean自动装配的模式。

7. lazy-init

    指定是否使用延迟加载模式。

8. init-method

    指定对象初始化时执行构造器后回调的方法名称。

9. destroy-method

    指定对象销毁时回调的方法名称。

### 3. bean 的作用域
bean的scope属性可以指定对象的作用域，有四种取值：
- singleton：指定对象是单例模式，在同一个IoC容器中只存在一个
- prototype：指定对象是多例模式，每次请求bean时都会创建一个新的实例
- request：指定对象的作用域在request请求中，该值在web应用中有效
- session：指定对象的作用域在session中，该值在web应用中有效

### 4. bean 的生命周期

bean 的生命周期如图所示：

![Spring bean 的生命周期](http://upload-images.jianshu.io/upload_images/4987579-1bbf14669aad1116.png)

生命周期执行的过程如下:

1. spring对bean进行实例化,默认bean是单例
2. spring对bean进行依赖注入
3. 如果bean实现了BeanNameAware接口,spring将bean的id传给setBeanName()方法
4. 如果bean实现了BeanFactoryAware接口,spring将调用setBeanFactory方法,将BeanFactory实例传进来
5. 如果bean实现了ApplicationContextAware()接口,spring将调用setApplicationContext()方法将应用上下文的引用传入
6. 如果bean实现了BeanPostProcessor接口,spring将调用它们的postProcessBeforeInitialization接口方法
7. 如果bean实现了InitializingBean接口,spring将调用它们的afterPropertiesSet接口方法,类似的如果bean使用了init-method属性声明了初始化方法,改方法也会被调用
8. 如果bean实现了BeanPostProcessor接口,spring将调用它们的postProcessAfterInitialization接口方法
9. 此时bean已经准备就绪,可以被应用程序使用了,他们将一直驻留在应用上下文中,直到该应用上下文被销毁
10. 若bean实现了DisposableBean接口,spring将调用它的distroy()接口方法。同样的,如果bean使用了destroy-method属性声明了销毁方法,则该方法被调用。


#### 1. bean 的初始化回调
bean的init-method属性可以指定bean在初始化时调用的方法 。该方法是一个void无参方法，必须先在bean所属的类中定义。

#### 2. bean 的销毁回调
bean的destroy-method属性可以指定bean在销毁后调用的方法。该方法是一个void无参数方法，必须先在bean所属的类中定义。

#### 3. 默认的初始化回调和销毁回调

#### 4. bean的生命周期在singleton和prototype下的不同

//TODO

在prototype的作用域下，bean 的destroy-method是无效的。因为在prototype下，同一类型的对象会有多个，当你每访问一次bean时，IoC容器都会创建一个新的对象，同时失去这个对象的控制权，不再管理这个对象，也就调用不了对象内的方法。

### 5. bean 的后置处理器
在类中实现BeanPostProcessor接口，并实现 `Object postProcessBeforeInitialization(Object bean, String beanName)` 方法和 `Object postProcessAfterInitialization(Object bean, String beanName)` 方法。

//TODO

### 6. bean 的定义继承
bean的parent属性可以指定一个bean成为此bean的父bean。子bean通过继承拥有父bean的属性值，子bean可以根据需要重写一些值，或者添加其他值。

Spring Bean 定义的继承与 Java 类的继承无关，但是继承的概念是一样的。

#### bean 定义模板
可以创建一个 bean 定义模板，让其他子bean都继承这个父bean。

在定义一个 bean 定义模板时，不能指定class属性的值，且需要指定abstract属性的值为 `true`。如下所示：
```xml
<bean id="beanTeamplate" abstract="true">
      <property name="java类的属性1" value="..."/>
      <property name="java类的属性2" value="..."/>
      <property name="java类的属性3" value="..."/>
</bean>

```
bean 定义模板不能被实例化，因为它不完整，它明确地被指定为抽象的。

### 7. bean 的实例化
可以通过三种方式实例化bean
#### 1. 直接构造器方式
使用构造器方式，只需指定bean的id和class属性
```xml
<bean id="..." class="所属的类"></bean>
```
#### 2. 静态工厂方法方式
使用静态工厂方法方式，需指定bean的factory-method属性。必须有一个静态工厂类，类中定义了返回其它类对象的静态方法。
```xml
<bean id="..." class="静态工厂类"  factory-method="返回其它类对象的静态方法"></bean>
```

#### 3. 实例工厂方法方式
使用实例工厂方法方式，需要先定义实例工厂的bean，再在另一个bean中指定factory-bean属性和factory-method属性。必须有一个工厂类，类中定义了返回其它类对象的方法。
```xml
<bean id="factory" class="实例工厂类"></bean>
<bean id="..." factory-bean="factory" factory-method="返回其它类对象的方法"></bean>
```

### 8. 依赖注入(DI)
在bean中实现依赖注入有以下两种方式：
- 基于构造器的方式
- 基于setter的方式

#### 1. 构造器的方式
使用构造器注入的方式：
```xml
<bean id="..." class="...">
    <constructor-arg index="0" name="" type="" value="" ref=""/>
    <constructor-arg index="1" name="" type="" value="" ref=""/>
    ...
</bean>
```

使用构造器方式，需要使用bean的子标签constructor-arg。constructor-arg标签有以下属性：
- index
- name
- type
- value
- ref

1. index属性name属性

    都可以用来指定此constructor-arg标签代表的是哪个参数。不同的是，index指定的是参数的位置(从0开始)；name指定的是参数的名称。二者可以同时存在，一般使用index。

2. type属性

    显式的指定构造函数参数的类型，

3. value属性和ref属性

    都可以用来指定参数要传递的值。不同的是，value是直接传值；ref传递的是对象引用。二者不可以同时存在。

#### 2. setter的方式
使用setter注入：
```xml
<bean id="..." class="...">
    <property name="" value="" ref=""/>
    <property name="" value="" ref=""/>
    <property name="" value="" ref=""/>
    ...
</bean>
```
使用setter方式需要使用bean的子标签property。property有以下三个属性：
- name：用来指定set方法的参数名称。
- value：用来指定参数要传递的值。
- ref：指定set方法的参数传递给对象的引用。

#### 使用 p-namespace 方式
如果有一个类有非常多的set方法，那么在xml文件中就要写非常多的property子标签，这会造成代码冗余严重。这时可以使用一种更为简洁的方式————`p-namespace`进行重写。

下面是标准的XML配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <bean id="john-classic" class="com.example.Person">
        <property name="name" value="John Doe"/>
        <property name="spouse" ref="jane"/>
    </bean>

    <bean name="jane" class="com.example.Person">
        <property name="name" value="John Doe"/>
    </bean>
</beans>
```
下面是使用了 p-namespace 方式的：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <bean id="john-classic" class="com.example.Person" p:name="John Doe" p:spouse-ref="jane"/></bean>

    <bean name="jane" class="com.example.Person" p:name="John Doe"/></bean>
</beans>
```
**注意：使用`p-namespace`需要添加名称空间`xmlns:p="http://www.springframework.org/schema/p"`！**

### 8. bean 的自动装配


## 3. Spring 注解
传统的Spring做法是使用xml文件来对bean进行注入或者是配置aop、事物，这么做有两个缺点：
- 如果所有的内容都配置在xml文件中，那么xml文件将会十分庞大；如果按需求分开xml文件，那么xml文件又会非常多。总之这将导致配置文件的可读性与可维护性变得很低。
- 在开发中在java文件和xml文件之间不断切换，是一件麻烦的事，同时这种思维上的不连贯也会降低开发的效率。


为了解决这两个问题，Spring引入了注解。注解与Java Bean紧密结合，既大大减少了配置文件的体积，又增加了Java Bean的可读性与内聚性。

### 1. 使用 Spring 注解
使用Spring注解之前需要在XML配置文件中添加名称空间和资源路径，然后再开启自动扫描功能，其中base-package为需要扫描的包(含子包)。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="..."/>
    ...
</beans>
```
### 2. Spring 常用注解
1. `@Configuration` 把一个类作为一个IoC容器，它的某个方法头上如果注册了@Bean，就会作为这个Spring容器中的Bean。
2. `@Scope` 作用域范围
3. `@Lazy(true)` 表示延迟初始化
4. `@Service` 用于标注业务层组件
5. @Controller 用于标注控制层组件（如struts中的action）
6. @Repository 用于标注数据访问组件，即DAO组件。
7. @Component 泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。
8. @Scope 用于指定scope作用域的（用在类上）
9. @PostConstruct 用于指定初始化方法（用在方法上）
10. @PreDestory 用于指定销毁方法（用在方法上）
11. @DependsOn 定义Bean初始化及销毁时的顺序
12. @Primary 自动装配时当出现多个Bean候选者时，被注解为@Primary的Bean将作为首选者，否则将抛出异常
13. @Autowired 默认按类型装配，如果我们想使用按名称装配，可以结合@Qualifier注解一起使用。如下：
@Autowired @Qualifier("personDaoBean") 存在多个实例配合使用
14. @Resource 默认按名称装配，当找不到与名称匹配的bean才会按类型装配。
15. @PostConstruct 初始化注解
16. @PreDestroy 摧毁注解 默认 单例  启动就加载
17. @Async 异步方法调用

## 4. Spring AOP
AOP(Aspect-Oriented Programming), 即 **面向切面编程**, 它与 OOP( Object-Oriented Programming, 面向对象编程) 相辅相成, 提供了与 OOP 不同的抽象软件结构的视角。

在 OOP 中, 我们以类(class)作为我们的基本单元, 而 AOP 中的基本单元是 Aspect(切面)。
### 1. AOP 概念
1. 连接点(Join point)：

    类里面有哪些点可以被增强（新增一些逻辑或功能），这些点可以是属性，方法或者构造器，在Spring中连接点只能是方法。

2. 切入点(Pointcut)：

    在类里有许多的连接点，实际增强的连接点称为切入点。

3. 通知/增强(advice)：

    扩展的功能或者逻辑，有以下5种增强：

    - 前置通知(before)：在切入点之前执行增强。
    - 返回后置通知(after-returning)：只有在切入点成功返回结果后，才执行增强。
    - 异常后通知(after-throwing)：只有当切入点执行时抛出异常时，才能执行增强。
    - 环绕通知(around)：可以在一个切入点的之前和之后添加不同的操作, 并且甚至可以决定何时, 如何, 是否调用匹配到的方法。
    - 后置(最终)通知(after)：不管切入点的执行结果如何都会执行增强。

4. 切面(Aspect)：

    由切入点和增强组成，把增强用到切入点的过程，也可以简单地认为, 使用 `@Aspect` 注解的类就是切面。

5. 目标对象(Target)：
    
    代理的目标对象（要增强的类）。

6. 引入(Introduction)：
    
    可以在不修改类文件的前提下，在**运行期**为类动态地添加一些属性或方法。

7. 织入(Weaving)：
    
    将切面和其他对象连接起来, 并创建代理对象的过程。

8. 代理(Proxy)：
    
    一个类被 AOP 织入增强, 就会产生一个结果类, 它是融合了原类和增强逻辑的代理类。

### 2. AOP 的原理
Spring AOP 默认使用标准的 JDK 动态代理(dynamic proxy)技术来实现 AOP 代理, 通过它, 我们可以为任意的接口实现代理.

如果需要为一个类实现代理, 那么可以使用 CGLIB 代理. 当一个业务逻辑对象没有实现接口时, 那么Spring AOP 就默认使用 CGLIB 来作为 AOP 代理了. 即如果我们需要为一个方法织入 advice, 但是这个方法不是一个接口所提供的方法, 则此时 Spring AOP 会使用 CGLIB 来实现动态代理. 鉴于此, Spring AOP 建议基于接口编程, 对接口进行 AOP 而不是类.

1. Java SE 动态代理
2. CGLib 动态代理
### 3. Spring 中 AOP 的实现
在 Spring 中使用 AOP 需要在XML文件中添加名称空间和资源路径
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">
</beans>
```

#### 1. xml 配置方式

```xml
<aop:config>
    <!-- 声明切面 -->
    <aop:aspect id="myAspect" ref="目标对象">
        <!-- 声明切入点，expression 用来匹配哪些方法是切入点 -->
        <aop:pointcut id="..." expression="execution(* com.xyz.myapp.service.*.*(..))"/>
        <!-- 前置通知 -->
        <aop:before pointcut-ref="切入点" method="执行的方法"/>
        <!-- 后置(最终)通知 -->
        <aop:after pointcut-ref="切入点" method="执行的方法"/>
        <!-- 返回后通知 -->
        <aop:after-returning pointcut-ref="切入点" returning="返回值类型" method="执行的方法"/>
        <!-- 异常后通知 -->
        <aop:after-throwing pointcut-ref="切入点" throwing="异常类型" method="执行的方法"/>
        <!-- 环绕通知 -->
        <aop:around pointcut-ref="切入点" method="执行的方法"/>
    </aop:aspect>
</aop:config>
```
#### 2. 注解方式
@AspectJ 是一种使用 Java 注解来实现 AOP 的编码风格。@AspectJ 风格的 AOP 是 AspectJ Project 在 AspectJ 5 中引入的, 并且 Spring 也支持@AspectJ 的 AOP 风格。

使用 @AspectJ 需要添加以下依赖库：
- aspectjrt.jar
- aspectjweaver.jar
- aspectj.jar
- aopalliance.jar

然后在xml文件中打开 Spring 的自动代理
```xml
<aop:aspectj-autoproxy/>
```
Spring 会自动选择代理方式，但是如果需要为一个类实现代理或者想强制使用CGLib生成代理，则可以指定 "proxy-target-class" 属性为 `true` 。
```xml
<aop:aspectj-autoproxy proxy-target-class="true"/>
```
1. 声明切面

    当使用注解 @Aspect 标注一个 Bean 后, 那么 Spring 框架会自动收集这些 Bean, 并添加到 Spring AOP 中, 例如:
    ```java
    @Component
    @Aspect
    public class MyTest {
        //TODO
    }
    ```
    注意, 仅仅使用@Aspect 注解, 并不能将一个 Java 对象转换为 Bean, 因此我们还需要使用类似 @Component 之类的注解。还有，如果一个 类被@Aspect 标注, 则这个类就不能是其他 aspect 的 **advised object** 了, 因为使用 @Aspect 后, 这个类就会被排除在 auto-proxying 机制之外。

2. 声明切入点

    一个 pointcut 的声明由两部分组成:
    - 一个 pointcut 表达式, 用来指定哪些方法执行是我们感兴趣的(即因此可以织入 advice)。
    - 一个方法签名, 包括方法名和相关参数。
    ```java
    @Pointcut("execution(* com.xyz.myapp.service.*.*(..))")
    private void businessService() {
        //TODO
    }
    ```
    **注意：此方法必须无返回值！**
3. 声明增强

    可以使用 `@{ADVICE-NAME}` 注释声明五个增强的任意一个，如下所示：
    ```java
    @Before("businessService()")
    public void doBeforeTask(){
        //TODO
    }
    @AfterReturning(pointcut = "businessService()", returning="retVal")
    public void doAfterReturnningTask(Object retVal){
        //TODO
    }
    @AfterThrowing(pointcut = "businessService()", throwing="ex")
    public void doAfterThrowingTask(Exception ex){
        //TODO
    }
    @Around("businessService()")
    public void doAroundTask(){
        //TODO        
    }
    @After("businessService()")
    public void doAfterTask(){
        //TODO
    }
    ```


---

**欢迎转载，转载请注明出处：[https://newkami.cn/2017/10/27/spring-note/](https://newkami.cn/2017/10/27/spring-note/)**