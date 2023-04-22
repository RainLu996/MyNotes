# Spring

> ​		[老杜Spring笔记：密码-mg9b](https://www.yuque.com/docs/share/866abad4-7106-45e7-afcd-245a733b073f?# )

## 一、Spring概述

### 1. 什么是Spring？

> ​		Spring：出现在2002年左右，用于简化企业应用程序的开发流程，使得开发者只需要关心**业务需求**，尽可能的不用再关心非业务逻辑代码（事务控制、安全日志等）。它提供对项目模块之间的管理，类和类之间的管理， 帮助开发人员创建对象，管理对象之间的关系。
>
> ​	    Spring的核心技术 IOC ， AOP 。能实现模块之间，类之间的解耦合。

> 主要包括以下七个模块：

- Spring Context：提供框架式的Bean访问方式，以及企业级功能（JNDI、定时任务等）；
- Spring Core：核心类库，所有功能都依赖于该类库，提供IOC和DI服务；
- Spring AOP：AOP服务；
- Spring Web：提供了基本的面向Web的综合特性，提供对常见框架如Struts2的支持，Spring能够管理这些框架，将Spring的资源注入给框架，也能在这些框架的前后插入拦截器；
- Spring MVC：提供面向Web应用的Model-View-Controller，即MVC实现。
- Spring DAO：对JDBC的抽象封装，简化了数据访问异常的处理，并能统一管理JDBC事务；
- Spring ORM：对现有的ORM框架的支持；



下图对应的是Spring 4.x的版本，5.x版本中Web模块的Portlet组件已经被废弃

|                        Spring基本模块                        |
| :----------------------------------------------------------: |
| <img src="imgs\Spring基本模块.png" alt="Spring基本模块" style="zoom: 80%;" /> |



### 2. 使用Spring的好处/Spring的优点是什么？

- 轻量：Spring是轻量的，基本的版本大约2MB
- 低侵入式设计：Spring属于**低侵入式**设计，代码的污染极低；
- 控制反转：Spring通过**控制反转**实现了<u>松散、解耦合</u>，由容器管理对象，对象的依赖关系。
- 面向切面编程(AOP)：Spring支持面向切面的编程，可以把**业务逻辑**和**交叉业务逻辑**分开
- Spring对于主流的应用框架提供了集成支持：Spring 不排斥各种优秀的开源框架，相反 Spring 可以降低各种框架的使用难度，Spring 提供了对各种优秀框架（如 Struts，Hibernate、MyBatis）等的直接支持。



## 二、控制反转（IOC）

### 1. 什么是IOC

> ​		**控制反转， 是一个理论，概念，思想。**它是指将对象的<u>创建，赋值以及对象生命周期的管理工作</u>都交给代码之外的**容器**实现， 而不是由<u>程序</u>直接进行对象操控。（控制权由程序代码转移到了 Spring 容器中，控制权发生了反转）

- #### 名词介绍
  
  - **<font color="red">控制：</font>**创建对象，给对象的属性赋值，管理对象之间的依赖关系的权利
  - **<font color="red">反转：</font>**把原来由开发人员在程序中管理，创建对象的权限转移给代码之外的**容器**实现。由容器代替程序本身管理对象、创建对象，给属性赋值……
  - **<font color="red">正转：</font>**由开发人员在程序中，主动创建对象、管理对象以及对象之间的依赖关系
  - **<font color="red">容器：</font>**是一个服务器软件， 一个框架（spring）



### 2. IOC是为了解决什么问题而提出的

​		**IOC解决对象之间的耦合问题**。例如当service层调用dao层时，传统方式下我们需要在service中new出dao层的具体实现类，这时当我们的实现类需要改变时，service层也需要做相应的改变，这就造成了service层和dao层的`强耦合`。而使用IOC实例化对象时，我们只需要关注调用的dao层的接口，在service中声明接口属性，具体的实现类在IOC容器中进行切换，因此不会产生对象中强耦合的情况。



### 3. 什么是DI

​		<u>IOC是一种思想，而DI是对这种思想的**具体实现**</u>。

​		依赖注入，是指程序在运行过程中，所需要使用的对象需要调用另一个对象协助时，无须在代码中手动创建，而是依赖于外部容器，由外部容器创建后传递给程序。



### 4. 注解与 XML 的对比

- `注解`优点是

    - 方便
    - 直观
    - 高效（代码少，没有配置文件的书写那么复杂）。 

- 缺点

    - 以**硬编码**的方式写入到 Java 代码中，修改是需要重新编译代码的。 

    

- `XML` 方式优点是

    - 配置和代码是分离的
    - 在 XML 中做修改，无需编译代码，只需重启服务器即可将新的配置加载。

- 缺点

    - 编写麻烦，效率低，大型项目过于复杂。



### ==5. 什么是IOC容器    To Be Continue~==

​		**IOC容器就是具有依赖注入功能的容器**。IOC容器负责实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。应用程序无需在代码中new相关的对象，而是直接使用由IOC容器进行组装的对象。在Spring中`BeanFactory`是IOC容器的顶层核心接口。

> ​		<u><font color="blue">Spring IOC容器需要通过**配置文件**才能知道哪些是它管理的对象。</font></u>
>
> ​		Spring IOC容器通过读取配置文件中的**配置元数据**，通过元数据对应用中的各个对象进行实例化及装配。一般使用基于xml配置文件进行配置元数据，而且Spring与配置文件完全解耦的，可以使用其他任何可能的方式进行配置元数据。比如注解、基于java文件的、基于属性文件的配置都可以。



==待续==

​		Spring 提供了两种 IoC 容器，分别为 `BeanFactory` 和 `ApplicationContext`。

- **BeanFactory（接口）：**是<u>基础类型的 IoC 容器</u>。提供了完整的 IoC服务支持。简单来说， BeanFactory 就是一个管理 Bean 的工厂，它主要负责初始化各种Bean，并调用它们的生命周期方法。

- **ApplicationContext：** 是 BeanFactory 的子接口，也被称为应用上下文。



### 6. \<context:annotation-config> 和 \<context:component-scan>

- **< context:annotation-config />：**仅能够在已经注册过的bean上面起作用。对于没有在Spring容器中注册的bean，它并不能执行任何操作。 



- **< context:component-scan base-package="XX.XX"/>** ：除了具有`<context:annotation-config />`的功能之外，还具有自动将带有@component，@service，@Repository等注解的对象注册到spring容器中的功能。 

> ​		所以：使用`<context:component-scan>`，功能更加全面。



- 详情参见：[context:annotation-config 和 context:component-scan的区别](https://blog.csdn.net/qq_39304851/article/details/109388295)



## 三、面向切面编程（AOP）

### 1. 什么是AOP

> ​		AOP(Aspect Oriented Programming，面向切面编程)：是对OOP(面向对象编程)的补充，是对面向对象编程的完善或者二次利用，**<font color="red">就是在面向对象的业务逻辑代码执行过程中动态<u>织入</u>交叉业务逻辑代码</font>**。
>
> ​		所谓`交叉业务逻辑代码`，就是指<u>通用的、与主业务逻辑无关的代码</u>。常见的就是做日志、事务、安全检查。在面向切面编程中，这些交叉业务逻辑代码被称为**<font color="blue">切面</font>**。

|             图解AOP思想              |
| :----------------------------------: |
| ![图解AOP思想](imgs\图解AOP思想.png) |

> ​		将`业务`看成是纵向的；将`切面`看成是横向的。



- 使用AOP的三要素

1. 切面的功能代码，使用切面去增强什么功能
2. 切面的执行位置，使用Pointcut表示切面执行的位置
3. 切面的执行时间，使用Advice表示执行时间；在目标方法之前，还是目标方法之后。



- 使用AOP的优点

1. 增强代码复用性
2. 代码易维护
3. 使开发者只关注于业务逻辑的实现



### 2. AOP的实现方式

> ​		 实现方式有四种，但是都是基于**静态代理**与**动态代理**这两大类来实现的。
>
> [谈谈你对IOC和AOP的理解及AOP四种实现方式_](https://blog.csdn.net/weixin_39615182/article/details/116904168)
>
> - **静态代理**：指使用AOP框架（例如`AspectJ`）提供的命令进行编译，从而<u>在编译阶段</u>就可生成AOP代理类，因此也称为<u>编译时增强</u>。
>   - **编译时编织**：使用特殊编译器实现
>   - **类加载时编织**：使用特殊的类加载器实现
> - **动态代理**：指<u>运行时在内存中</u>临时生成AOP动态代理类。因此也被称为<u>运行时增强</u>。
>   - **JDK动态代理**：利用**<font color="green">反射</font>**来创建代理类，并且<u>要求被代理的类必须实现一个接口</u>。JDK动态代理的核心是 `Invocation Handler接口`、 `Proxy类`和`Method类`。
>   - **CGLIB动态代理**：第三方的工具库（非JDK自带），可以创建代理对象，原理是**<font color="green">继承</font>**。 通过继承目标类，创建子类。这个子类就是**代理对象**。 <u>要求目标类不能是final的， 方法也不能是final的</u>。





### 3. AOP的相关术语

> ​		**<font color="blue">切面 = 通知 + 切入点</font>**

|           术语            |                             说明                             |
| :-----------------------: | :----------------------------------------------------------: |
|   切面<br />（Aspect）    |     泛指**交叉业务逻辑**。实际就是对主业务逻辑的一种增强     |
| 连接点<br />（Joinpoint） | 连接业务方法和切面，是切面**可以织入**的位置（织入到哪个业务方法？） |
| 切入点<br />（Pointcut）  | 匹配若干个连接点的表达式。<br />是程序在执行过程中，切面**真正织入**的位置。 |
|   织入<br />（Weaving）   | 将切面代码插入到目标对象的过程（创建代理对象）<br />**（目标对象就是指被代理的原始对象）** |
|   通知<br />（Advice）    | 是切面功能的实现，用来定义交叉业务逻辑代码。<br />是交叉业务逻辑代码（切面）织入到目标对象的<u>时间点</u>。 |
|   顾问<br />（Advisor）   | 是切面的另一种实现，能够将通知以**更为复杂**的方式织入到目标对象中。<br />是将通知包装为更复杂切面的装配器。**不仅指定了切入时间点，还可以指定具体的切入点。** |

|             图解AOP术语              |
| :----------------------------------: |
| ![图解AOP术语](imgs\图解AOP术语.png) |



### 4. AspectJ 中常用的通知有五种类型：
（1）前置通知（`Before`）：在目标方法【之前】执行的
（2）后置通知（`AfterReturning`）：在目标方法【之后】（已经return了）执行的
（3）环绕通知（`Around`）：在目标方法【之前】、【之后】都能增强功能
（4）异常通知（`AfterThrowing`）：在目标方法【抛出异常之后】执行的。
（5）最终通知（`After`）：在目标方法之后执行。并且一定会执行，有异常也会执行。**一般用来做资源的关闭工作**
（6）定义切入点（`Pointcut`）：将多次使用的切入点表达式集中定义，提高复用性。而不做业务增强。



### 5. Spring AOP 和 AspectJ AOP 的区别

- Spring AOP 属于**运行时增强（动态代理）**，而 AspectJ 是**编译时增强（静态代理）**。 Spring AOP 基于**代理(Proxying)**，而 AspectJ 基于**字节码操作(Bytecode Manipulation)**。Spring AOP仅支持<u>方法级别</u>的 Pointcut；而AspectJ提供了完全的AOP支持，它还支持<u>属性级别</u>的Pointcut。

- Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，如果我们的切面比较少，那么两者性能差异不大。但是，<u>当切面太多的话，最好选择 AspectJ AOP，它比Spring AOP 快很多</u> 。



### 6. 切入点表达式

- 语法

```txt
execution([访问控制权限修饰符] 返回值类型 [全限定类名]方法名(形式参数列表) [异常])
```



- 访问控制权限修饰符
  - 可选项
  - 没写，就是4个权限都包括
  - 写public就表示只包括公开的方法



- 返回值类型
  - 必填项
  - \* 表示返回值类型任意



- 全限定类名
  - 可选项
  - 两个点“..”代表当前包以及子包下的所有类
  - 省略时表示所有的类



- 方法名
  - 必填项
  - *表示所有方法
  - set*表示所有的set方法



- 形式参数列表
  - 必填项
  - () 表示没有参数的方法
  - (..) 参数类型和个数随意的方法
  - (*) 只有一个参数的方法
  - (*， String) 第一个参数类型随意，第二个参数是String的



- 异常
  - 可选项
  - 省略时表示任意异常类型



### 7. 在Spring中配置使用AspectJ AOP


- XML配置方式

```xml
<!--

声明【自动代理生成器】：使用aspectj框架内部的功能，创建目标对象的代理对象。

创建代理对象是在【内存】中完成的，本质上就是在内存中修改目标对象的结构，使之成为一个代理对象
 所以：代理对象就是一个被修改之后的目标对象；只不过程序看起来仍然是一个目标对象。
 
 aspectj-autoproxy：会把Spring容器中所有的【目标对象】一次性都生成【代理对象】。
 属性proxy-target-class="true"：指定无论_有没有接口_，都使用CGLIB进行动态代理。
  如果为默认值："false"。则表示如果【有接口】，就使用JDK的动态代理。而如果【没有接口】，则自动使用CGLIB的动态代理方式
 -->
 <aop:aspectj-autoproxy/>
 <!--<aop:aspectj-autoproxy proxy-target-class="true"/>-->
```

 

 - JavaConfig配置方式

 ```java
@Configuration
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class Spring6Configuration {
}
 ```

 



- @Order注解的使用

> ​		业务流程当中不一定只有一个切面，可能有的切面控制事务，有的记录日志，有的进行安全控制。如果多个切面的话，顺序该如何控制：**可以使用`@Order`注解来标识切面类，为`@Order`注解的`value`属性指定一个<u>整数型</u>的数字，数字越小，优先级越高**。



------

[Spring源码学习---基于注解配置Aop的aspectj-autoproxy解析](https://blog.csdn.net/weixin_45480785/article/details/113819166)





## 四、Spring事务

参考文档：

- [Spring事务](https://blog.csdn.net/weixin_44688301/article/details/115702723)

<hr>

### 1. 什么是事务

​		事务是一种机制、是多个**<u>DML</u>**数据库操作命令的操作序列，是一个最小工作单元、一个完整的业务逻辑，不可再分。在事务中的一系列DML命令要么同时执行成功，要么同时执行失败。



### 2. 编程式事务与声明式事务

[(28条消息) Spring中的编程式事务与声明式事务_XY-JAVA的博客-CSDN博客_声明式事务](https://blog.csdn.net/m0_66918076/article/details/124390848)

[(28条消息) 事务 编程式事务和声明式事务的优缺点_fastjson_的博客-CSDN博客_编程事务和声明事务](https://blog.csdn.net/bbj12345678/article/details/109688950)

- **编程式事务：**通过编程的方式管理事务。

  ​		编程式事务管理使用`TransactionTemplate`或者直接使用底层的`PlatformTransactionManager`来进行实现。对于编程式事务管理，spring推荐使用`TransactionTemplate`。但实际开发中，编程式事务很少用，因为它对代码具有**侵入式**的影响。虽然灵活度很高，但是难以维护！

  

- **声明式事务：**通过框架来进行事务管理。

  ​		这种方式是建立在`AOP`之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。它最大的优点就是不需要通过编程的方式管理事务，这样就<u>不需要在业务逻辑代码中掺杂事务管理的代码</u>，只需**在配置文件中**做相关的事务规则声明或通过**基于@Transactional注解**的方式，便可以将事务规则应用到业务逻辑中。



> - **基于注解**实现事务的方式用起来简单、方便、灵活，所以**中小型项目**用注解的方式会更加方便、直观。
>
> - **基于配置**实现事务的方式适合**大型项目**，因为大型项目中往往有很多的类、方法，需要大量的配置事务，使用注解将造成维护不便（因为类太多，总会有考虑不全的地方）。这时，使用AspectJ框架功能，在spring配置文件中声明类，方法需要的事务。这种方式**让业务方法和事务配置完全分离**。



### 3. 为什么Spring要进行事务管理

> ​		对于不同的数据库访问技术，它们处理事务的对象，方法各有千秋。这就造成当我们使用不同的数据库访问技术进行事务操作时，需要了解不同的数据库访问技术在使用事务时的机制和原理。这增加了开发成本，提高了开发难度。
>
> ​		 Spring提供了一种处理事务的**统一模型**， **能使用统一的方式完成多种不同数据库产品的事务处理。**
>
> 
>
> ​		==**注意：Spring只是提供一种管理事务的能力|统一的平台。真正处理事务的是不同的数据库访问技术。**==
>
> > - **事务性资源：**如关系型数据库Mysql、Oracle、某些消息队列中间件等，因为它们**本身支持事务**，也能够处理事务。
> > - Spring不是事务性资源，但是它可以**管理事务性资源**，所以Spring和事务之间是**<font color="blue">管理关系</font>**。

|                        Spring事务模型                        |
| :----------------------------------------------------------: |
| <img src="imgs\Spring事务模型.png" alt="Spring事务模型" style="zoom:150%;" /> |



### 4. Spring事务三要素

- **数据源**：表示具体的<u>事务性资源</u>（支持事务的产品），是事务的**<font color="red">真正处理者</font>**，如MySQL等。（注意：只要`org.mybatis.spring.SqlSessionFactoryBean`引用的数据源与`DataSourceTransactionManager`引用的<font color="green">数据源一致</font>，<u>MyBatis就会自动参与到Spring事务管理中</u>，无需额外配置。）

- **事务管理器**：像一个大管家，从整体上管理事务的处理过程，如事务开启、事务提交、事务回滚等。

- **事务应用和属性配置**：像一个标识符，表明哪些方法要参与事务，如何参与事务，以及一些相关属性如隔离级别、超时时间等。

---



[Spring事务相关接口解析：PlatformTransactionManager、TransactionDefinition和TransactionStatus](https://blog.csdn.net/unbelievevc/article/details/126340197)

`PlatformTransactionManager` 接口有两个常用的实现类：

- **DataSourceTransactionManager**：使用 JDBC（MyBatis底层使用JDBC） 进行数据库操作时使用。
-  **HibernateTransactionManager**：使用 Hibernate 进行数据库操作时使用。



### 5. 声明式事务实现方式

> - **Spring管理事务的处理逻辑：**
>    1）Spring会首先检查方法抛出的异常是不是在`rollbackFor属性`中；
>       如果异常在`rollbackFor`的列表之中，且只要是`Throwable的子类异常`，一定会回滚！**不管是受检还是非受检。**
>    2）如果抛出的异常不在`rollbackFor`列表之中，Spring会判断异常是不是`RuntimeException`或者`Error`；如果是，一定rollback。**<font color="blue">注意：如果是受检异常，则直接提交。（王八的屁股！！！）</font>**
>
> 
>
> #### <font color="red">													事务属性的注意点</font>
>
> 1. 事务的超时时间指的是哪段时间
>
>    ​		**是在当前事务中，==最后一条DML语句==执行之前的时间。如果最后一条DML语句后面很有很多业务逻辑，这些业务代码执行的时间不被计入超时时间。** **小技巧：如果想让整个方法的所有代码都计入超时时间的话，可以在方法最后一行添加一行无关紧要的DML语句。**
>
> 2. 只读事务
>
>    ​		将当前事务设置为只读事务 ，在该事务执行过程中只允许select语句执行，`delete` `insert` `update`均不可被执行，出现即抛出异常。该特性的作用是：**启动Spring的优化策略。提高select语句执行效率**。（这也就解释清楚了一个疑问：select语句不对表数据造成安全威胁，还加个只读岂不是蛋疼？）**如果该事务中确实没有增删改操作，建议设置为只读事务。**





#### 1）使用@Transactional注解

```java
@Transactional(
            propagation = Propagation.REQUIRED，   // 默认值
            isolation = Isolation.DEFAULT，        // 默认值
            readOnly = false，                     // 默认值
            rollbackFor = {
                    NoEnoughGoodsException.class， NullPointerException.class， IOException.class
            }
    )
```



#### 2）使用XML进行AOP配置

```xml
<!--配置事务管理器-->
<bean id="transactionManager" 	
      class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="myDataSource" />
</bean>


<tx:advice id="myAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <!-- 优先级：一级。写完整方法名 -->
        <tx:method name="buyGood" propagation="REQUIRED" isolation="DEFAULT" 
                   read-only="false"
                   rollback-for="java.lang.NullPointerException， ……"/>

        <!-- 优先级：二级。使用命名模式 + 通配符 -->
        <!-- 命名规则：带add的添加方法 -->
        <tx:method name="add*" propagation="REQUIRED" isolation="DEFAULT" 
                   read-only="true"
                   rollback-for="java.lang.NullPointerException， ……"/>

        <!-- 三级：缺省匹配。使用*代表可以匹配任意方法。 -->
        <tx:method name="*" propagation="REQUIRED" isolation="DEFAULT" 
                   read-only="false"
                   rollback-for="java.lang.NullPointerException， ……"/>
    </tx:attributes>


    <!-- 在以上的配置方式中存在着缺点：
                【任何包】中的符合命名规范的方法，都会被配置事务。这是不符合咱们的目的的。
                如果我们只需要给 service 包中的类配置事务，那就得按照以下方法配置切面表达式
         -->
</tx:advice>

<aop:config>
    <!-- 配置切入点表达式：指定哪些包中的类要使用事务
                id：切入点表达式的名称，唯一值
                expression：切入点表达式，指定哪些类要使用事务，aspectj会去创建代理对象
        -->
    <aop:pointcut id="servicePT" expression="execution(* *..service..*.*(..))"/>

    <!-- 配置【增强器】：关联advice和pointcut
                 advice-ref：【通知】，引入<tx:advice>和</tx:advice>之间的配置内容
                 pointcut-ref：切入点表达式的id
        -->
    <aop:advisor advice-ref="myAdvice" pointcut-ref="servicePT"/>
</aop:config>	
```



### 6. 事务的传播行为

#### 1）什么是事务的传播行为

> ​		在service类中有a()方法和b()方法，a()方法上有事务，b()方法上也有事务，当a()方法执行过程中调用了b()方法，事务是如何传递的？合并到一个事务里？还是开启一个新的事务？这就是**事务传播行为**。



#### 2）七种事务传播行为

- `REQUIRED`：支持当前事务，如果不存在就新建一个(默认)**<font color="red">【没有就新建，有就加入】</font>**
- `SUPPORTS`：支持当前事务，如果当前没有事务，就以非事务方式执行**<font color="red">【有就加入，没有就不管了】</font>**
- `MANDATORY`：必须运行在一个事务中，如果当前没有事务正在发生，将抛出一个异常**<font color="red">【有就加入，没有就抛异常】</font>**
- `REQUIRES_NEW`：开启一个新的事务，如果一个事务已经存在，则将这个存在的事务挂起**<font color="red">【不管有没有，直接开启一个新事务，开启的新事务和之前的事务不存在嵌套关系，之前事务被挂起】</font>**
- `NESTED`：如果当前正有一个事务在进行中，则该方法应当运行在一个嵌套式事务中。被嵌套的事务可以独立于外层事务进行提交或回滚。如果外层事务不存在，行为就像REQUIRED一样。**<font color="red">【有事务的话，就在这个事务里再嵌套一个完全独立的事务，嵌套的事务可以独立的提交和回滚。没有事务就和REQUIRED一样，新建一个事务。】</font>**
- `NOT_SUPPORTED`：以非事务方式运行，如果有事务存在，挂起当前事务**<font color="red">【不支持事务，存在就挂起】</font>**
- `NEVER`：以非事务方式运行，如果有事务存在，抛出异常**<font color="red">【不支持事务，存在就抛异常】</font>**

> ​		我知道你有一个疑问：
>
> - NESTED是为被嵌套的方法开启了一个子事务，这个事务与父类**使用的是同一个连接**。可以独立的提交与回滚。
>- REQUIRES_NEW是使用一个全新的事务，这个事务使用的是**另外一条全新的连接**。可以独立的提交与回滚。但是会挂起已有的事务。
> - 加入事务：加入已有的事务中执行，不可以独立的提交与回滚。



### 7. 事务的ACID特性

#### 1) 原子性（atomicity）

> ​		**原子性是指事务是一个不可分割的最小工作单元， ⼀个事务中的多个DML操作，要么同时执行成功，要么同时执行失败，不存在中间的状态**。

​		如果无法保证原子性会怎么样？就会出现数据不一致的情形，A账户减去100元成功，而B账户增加100元操作失败，系统将无故丢失100元。



#### 2) 一致性（consistency）

> ​		**在一个事务中的数据，被操作前后的 合法性状态 不会被改变**。所谓的合法性状态是指在多个层面上的对数据的约束。可以是数据总量，可以是表中对字段定义的约束……



**举例1：**A账户有200元，转账300元出去，此时A账户余额为-100元。你自然就发现此时数据是不一致的，为什么呢？因为你定义了一个状态，余额这列必须>=0。

**举例2：**A账户有200元，转账50元给B账户，A账户的钱扣了，但是B账户因为各种意外，余额并没有增加。你也知道此时的数据是不一致的，为什么呢？因为你定义了一个状态，要求A+B的总余额必须不变。

**举例3：**在数据表中我们将`姓名`字段设置为`唯一性约束`，这时当事务进行提交或者事务发生回滚的时候，如果数据表的姓名不唯一，就破坏了事物的一致性要求。



#### 3) 隔离型（isolation）

> ​		事务的隔离性是指在**并发**的条件下，当**多个事务**对**同一张表**进行操作的时候，一个事务的执行`不能被其他事务干扰`，即一个事务内部的操作及使用的数据对`并发`的其他事务是隔离的。



#### 4) 持久性（durability）

​		持久性是事务结束的一个标志。一个事务一旦被提交，它对数据库中数据的改变就是 **永久性** 的 ，接下来的<u>其他操作</u>和<u>数据库故障</u>不应该对其有任何影响。



#### 5) 总结

​		ACID是事务的四大特征，在这四个特性中，**原子性是基础，隔离性是手段，一致性是约束条件， 而持久性是我们的目的**。

​		数据库事务，其实就是数据库设计者为了方便起见，把需要保证`原子性`、`隔离性`、`一致性`和`持久性`的一个或多个数据库操作称为一个事务。



### 8. 事务的隔离级别

> ​		事务隔离级别类似于教室A和教室B之间的那道墙，隔离级别越高表示墙体越厚。隔音效果越好。
>
> ​		即：**事务隔离级别的存在，就是为了在多事务并发操作同一张表的情况下，隔离多个事务之间的干扰而存在的一种<font color="green">机制</font>。**

① 数据库中读取数据存在的三大问题：（三大读问题）

[不可重复读和幻读的区别是什么](https://m.php.cn/article/474519.html)

> ​		区别：**不可重复读的重点是修改**；同样的条件，第1次和第2次读取的值不一样。**幻读的重点在于数据的新增**；同样的条件， 第1次和第2次读出来的记录数不一样。
>
> ​		从控制角度来看，**不可重复读只需要锁住满足条件的记录，幻读要锁住满足条件及其相近的记录**。

- **脏读：读取到没有提交到数据库的数据，叫做脏读。**
- **不可重复读：在同一个事务（未提交）中，第一次和第二次读取的数据不一样。**
  - **<font color="green">不可重复读的重点是修改（update、delete）</font>**
  - 与幻读一样，表现为两次读取的结果不一致
  - 只需要锁住<font color="#5b89fe">满足条件</font>的记录
- **幻读：读到的数据是假的。**（大白话就是：<u>我脑袋里认为的表中的数据和实际上的当前表中的数据不一致</u>。比如说A事务DQL了一下表中的数据；与此同时，B事务DML了表中的数据。所以此时A事务仍然认为当前表中的数据就是刚刚DQL得到的数据，但不然，已经物是人非……）幻读是不可重复读的**数据增加**的特殊情况。
  - **<font color="green">幻读的重点在于数据新增（insert）</font>**
  - 与不可重复读一样，表现为两次读取的结果不一致
  - 需要锁住<font color="#5b89fe">满足条件</font>及其<font color="#5b89fe">相近</font>的记录



② 事务隔离级别包括四个级别：

- 读未提交：`READ_UNCOMMITTED`

  - 这种隔离级别，存在脏读问题，所谓的**脏读**(dirty read)表示能够读取到<u>其它事务未提交</u>的数据。
  - 须知：**脏写**(dirty write)表示能够修改<u>其它事务修改过</u>的数据

- 读已提交：`READ_COMMITTED`（Oracle默认）

  - 解决了脏读问题，其它事务提交之后才能读到，但存在不可重复读问题。

- 可重复读：`REPEATABLE_READ`（MySQL默认）

  - 解决了不可重复读，可以达到可重复读效果，只要当前事务不结束，读取到的数据一直都是一样的。但存在幻读问题。

- 序列化：`SERIALIZABLE`

  - 解决了幻读问题，事务排队执行。不支持并发。大白话就是：如果操作同一张表的两个事务A和B，只要A事务不结束（commit），B事务就不会开始（被阻塞）。



> ​		对于可重复读来说：只要当前事务不结束，读取到的数据就一直会是一样的。（可重复读）但不代表我读取到的数据都是真实的（幻读）。
>
> ​		对于序列化读来说：只能一个事务一个事务的执行，若其中某个事务未结束，另外的事务都会处于阻塞状态。因此，只要有事务在成功执行，数据就是真实的，即不存在幻读。注意：神奇的一点是，连DQL都会被阻塞。

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
| :------: | :--: | :--------: | :--: |
| 读未提交 |  有  |     有     |  有  |
| 读已提交 |  无  |     有     |  有  |
| 可重复读 |  无  |     无     |  有  |
| 序列化读 |  无  |     无     |  无  |



> ​		隔离级别越高，并发的性能就越低，所以我们需要对此做出权衡。 





## 六、JavaConfig

### 1. 什么是 JavaConfig？

- Spring JavaConfig 是 Spring 社区的产品，Spring 3.0引入了它，它提供了配置 Spring IOC 容器的纯Java 方法。因此它有助于避免使用 XML 配置。使用 JavaConfig 的优点在于：
    - **面向对象的配置**。由于配置被定义为 JavaConfig 中的类，因此用户可以充分利用 Java 中的面向对象功能。一个配置类可以继承另一个，重写它的@Bean 方法等。
    - **减少或消除 XML 配置**。基于依赖注入原则的外化配置的好处已被证明。但是，许多开发人员不希望在 XML 和 Java 之间来回切换。JavaConfig 为开发人员提供了一种纯 Java 方法来配置与 XML 配置概念相似的 Spring 容器。从技术角度来讲，只使用 JavaConfig 配置类来配置容器是可行的，但实际上很多人认为将JavaConfig 与 XML 混合匹配是理想的。
    - **类型安全和重构友好**。JavaConfig 提供了一种类型安全的方法来配置 Spring容器。由于 Java 5.0 对泛型的支持，现在可以按类型而不是按名称检索 bean，不需要任何强制转换或基于字符串的查找。
- 常用的JavaConfig注解：
    - @Configuration：在类上标明此注解，表示这个类是`配置类`
    - @ComponentScan：在配置类上添加 @ComponentScan 注解。该注解默认会扫描该类所在的包下所有的配置类，对应 <context:component-scan="包名" >。
    - @Bean：对应< bean id=“objectMapper” class=“org.codehaus.jackson.map.ObjectMapper” />
    - @EnableWebMvc：对应< mvc:annotation-driven >
    - @ImportResource： 对应< import resource=“applicationContext-cache.xml”>
    - @ImporResource：对应< import resources="其他配置文件"/>
    - @PropertySource：对应< context:property-placeholder location="classpath:XXXX.properties"/>

---



### 2. 常用注解集锦

#### 1）@Configuration

​		JavaConfig：使用**Java类**作为XML配置文件的替代品， 是一个纯Java的Spring容器的**<font color="red">配置方式</font>**。<u>在这个Java类中可以创建Java对象，把对象放入Spring容器中（注入到容器）</u>。 我们需要使用`@Configuration`和`@Bean`注解来进行实现。



- `@Configuration` ： 放在一个类的上面，表示这个类是作为**配置文件**使用的。
    - 若配置类中的各组件之间**无依赖关系**，用`Lite模式`可以加速容器启动的过程
    - 若配置类中的各组件之间**有依赖关系**，用`Full模式`

> ​		为什么Lite模式的高效率呢？因为使用Full模式时，每次在调用@Bean方法时，都会检查容器中是否存在当前bean，存在则返回此bean；不存在，则新建一个bean。而使用Lite方法时，压根不会去检查容器中是否会存在当前bean，而是直接创建一个bean返回。

详情请参见：[Spring配置类之full和lite模式](https://blog.csdn.net/tales522/article/details/117885968)

- @Bean：声明对象，把对象注入到容器中。

```java
/**
 * Configuration注解：表示当前类是作为Spring配置文件来使用的
 *    1、在这个类中有很多被@Bean注解的方法，每个方法的返回值都是对象，并且会被放入到Spring容器中。
 *    2、配置类本身也是组件
 *    3、proxyBeanMethods：代理bean的方法。通过CGLIB动态代理为当前【配置类】生成代理类。
 *      Full(proxyBeanMethods = true)【保证每个@Bean方法被调用多少次返回的组件都是单实例的】
 *      Lite(proxyBeanMethods = false)【每个@Bean方法无论被调用多少次，返回的组件都是新创建的】
 */
@Configuration
public class SpringConfig {
    /**
     * 定义方法， 方法的返回值是对象。
     * @Bean：表示把对象注入到容器中。
     * 位置： 方法的上面
     * @Bean 没有使用属性，默认对象名称是方法名
     */
    @Bean
    public Student createStudent(){
        Student student = new Student();
        student.setId(1002);
        student.setName("lujun");
        student.setAge(29);
        return student;
    }

    // name ：指定对象的名称
    @Bean(name = "myStudent2")
    public Student makeStudent(){
        Student student = new Student();
        student.setId(1003);
        student.setName("xiaolu");
        student.setAge(30);
        return student;
    }
}

```



- 获取在JavaConfig中创建的Bean对象：`AnnotationConfigApplicationContext(JavaConfig类.class)`

```java
@Test
public void test() {
    // 没有 xml 配置文件，使用 java 类代替 xml 配置文件 的作用
    ApplicationContext ac = new AnnotationConfigApplicationContext(ImportResourceConfig.class);

    System.out.println(ac.getBean("myStudent"));
}
```





#### 2）@ImporResource

@ImportResource 的作用是导入**XML配置文件**，等价于在xml中使用如下标签：

```xml
<import resources="其他配置文件"/>
```



例如：

```java
@Configuration
@ImportResource(value ={ "classpath:applicationContext.xml"，"classpath:beans.xml"})
public class SpringConfig {
}
```



#### 3）@PropertySource

@PropertySource：读取properties属性配置文件。 使用属性配置文件可以实现外部化配置 ，在程序代码之外提供数据。相当于在XML文件中使用：

```xml
<context:property-placeholder location="classpath:XXXX.properties"/>
```



步骤：

1. 在resources目录下，创建properties文件， 使用k=v的格式提供数据
2. 在PropertySource指定properties文件的位置
3. 使用@Value（value="${key}"）

```java
@Configuration
@ImportResource(value ={ "classpath:applicationContext.xml"，"classpath:beans.xml"})
@PropertySource(value = "classpath:config.properties")
@ComponentScan(basePackages = "com.lujun61.vo")
public class SpringConfig {
}
```



#### 4）@ConfigurationProperties

​		`@ConfigurationProperties`： 把配置文件的数据映射为java对象。不需要再手动使用@Value注解进行基本类型数据的赋值了。



1. 使用方式一：`@Component` + `@ConfigurationProperties`联合使用

属性：**prefix：**对应配置文件中的key以prefix开头的配置项。

```java
@Component
@ConfigurationProperties(prefix = "school")
public class SchoolInfo {

    private String name;

    private String website;

    private String address;
	// get set ……
}
```



application.properties

```properties
#配置端口号
server.port=8082
#context-path
server.servlet.context-path=/myboot

#自定义key=value
school.name=动力节点
school.website=www.bjpowernode.com
school.address=北京的大兴区
```



- 使用 `ConfigurationProperties` 注解之后，IDEA 会出现一个警告，不影响程序的执行。可以选择性解决。

> ​		目的其实就是在yml中配置属性时，有提示窗口。

```xml
<!--解决使用@ConfigurationProperties 注解出现警告问题-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>


<!--由于不参与项目运行，建议打包的时候排除掉它-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <exclude>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-configuration-processor</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
```



2. 使用方式二：`@EnableConfigurationProperties` + `@ConfigurationProperties`

> ​		存在的意义是：给第三方的bean自动赋值。因为第三方的bean无法被@Component所注解。

```java
@Configuration
/**
 * @description 使用@EnableConfigurationProperties + @ConfigurationProperties给第三方bean自动绑定值
 */
//@ConfigurationProperties(prefix = "custom")   // 将注解写在类上时，不会奏效
public class CustomInfoConfig {
    // 必须将注解写在被@Bean注解的方法之上！！！
    @ConfigurationProperties(prefix = "custom")
    @Bean
    public CustomInfo customInfo2() {
        return new CustomInfo();
    }
}
```



## 七、Spring面试

### 1. Spring中bean的生命周期

- **创建过程**：实例化Bean，并设置Bean的属性，根据其实现的 `Aware接口`（主要是BeanFactoryAware接口， BeanfactoryAware， ApplicationContextAware）设置依赖信息，接下来调用 `BeanPostProcess`的 `postProcessBeforelnitialization`方法，完成 <u>bean初始化前</u>的自定义逻辑；`afterPropertiesSet`方法用来做一些属性被设定后的自定义的事情；调用Bean自身定义的init方法，去做一些初始化相关的工作；然后再调用 `postProcessAfterInitialization`去做一些<u>bean初始化之后</u>的自定义工作。此时，Bean初始化完成，可以使用这个Bean了。

- **销毁过程**：如果实现了 `DisposableBean`的 `destroy`方法，则调用它，如果实现了自定义的销毁方法，则调用它。



### 2. Spring中bean的作用域

- Spring Bean默认作用域是单例还是多例?

​		在Spring中，Bean的默认作用域是单例（`singleton`）。可以在`<bean>元素`的`scope属性`里设置bean的作用域。

​		

- Spring Bean的作用域有几种?    **<font color="blue">8种</font>**。其实记住前四种的使用就了不起了。

1. `singleton`：默认值。<u>bean实例在IoC容器被创建时创建，而且是单例的。</u>每次通过`getBean`得到的都是同一个bean。
2. `prototype`：原型的。当IoC容器被创建时并不会创建该bean，而是在每次调用`getBean()` 方法时再实例化该bean，而且每次调用`getBean()`得到的都是不同的bean实例。
3. `request`：每一次HTTP请求都会产生一个新的实例，并且该bean仅在当前`HTTP请求`内有效。**（仅限于在WEB应用中使用）**
4. `session`：每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前`HTTP Session`内有效。**（仅限于在WEB应用中使用）**

> ​		注意：仅在**Web项目（集成了SpringMVC）中**，`request作用域`和`session作用域`才可用！！！

|               普通Spring项目               |
| :----------------------------------------: |
| ![普通Spring项目](imgs\普通Spring项目.png) |



|                SpringMVC-WEB项目                 |
| :----------------------------------------------: |
| ![SpringMVC-WEB项目](imgs\SpringMVC-WEB项目.png) |



5. `global session`：**portlet应用中专用的**。如果在Servlet的WEB应用中使用global session的话，和session一个效果。（portlet和servlet都是规范。servlet运行在servlet容器中，例如Tomcat。portlet运行在portlet容器中。）**（仅限于在portlet应用中使用）**
6. `application`：一个应用对应一个Bean。**（仅限于在WEB应用中使用）**。
7. `websocket`：一个websocket生命周期对应一个Bean。**（仅限于在WEB应用中使用）**。
8. `自定义scope`：很少使用。



### 3. @Repository，@Service，@Controller 注解有什么区别？

> ​		@Repository、@Service、@Controller 是对@Component 注解的细化，是用来给项目分层，标注不同层的对象的。各自拥有不同的含义，表示被注解的类所实现的功能是不一样的。

- **@Repository：用在【持久层】类的上面，表示创建的是持久层类的对象。**
- **@Service：用在【业务逻辑层】类的上面，表示创建的是业务逻辑层对象。**
- **@Controller：用在【表现层】类的上面，表示创建的是表示层对象。**

- **@Component：用在无法归类于三层中的类的上面。**

[彻底弄懂@Controller 、@Service、@Component - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/454638478)



### 4. @Component系列 和 @Bean 的区别

> ​	不单指@Component注解，还有@Repository，@Service，@Controller

​		使用`@Component` 和 `@Bean` 的目的都是将bean**<font color="blue">注册</font>**到Spring容器中。只是它们出现的位置、适用范围存在着差异。

- @Component作用于类，表示当前类会被Spring的组件扫描器扫描到（当然，前提是需要开启组件扫描器）并**<u>自动注册</u>**成一个bean至Spring容器中（当前类与被装配的bean存在一对一的映射关系），`类中的属性`可以享受到Spring的**<u>自动装配</u>**机制（使用@Autowired&@Qualifier、@Resource注解）。
- @Bean作用于方法，用于显示声明**<u>单个bean</u>**，能做到将bean的声明与<u>类的定义</u>完全分离，但是无法对bean中属性进行自动装配了。



> ​		各自的应用场景：当我们想将<u>**第三方的类**</u>注册到我们的应用中的时候，`@Component注解`无能为力。因为缺少源代码，自然也就无法在类（源代码）上加上注解进行bean注册。此时，就需要使用`@Bean注解`来进行**<font color="green">第三方类bean的创建</font>**。



### 5. Spring装配

​		当 bean 在 Spring 容器 中组合在一起时，它被称为`装配`或 `bean 装配`。Spring 容器需要知道需要什么 bean 以及容器应该如何使用依赖注入来将 bean 绑定在一起，同时装配 bean。

​		Spring 容器能够**自动装配** bean。也就是说，可以通过检查 BeanFactory 的内容让 Spring 自动解析 bean 的协作者。



> - Spring支持三种注册Bean及装配Bean的方式
>     - 显式地在Java代码中注册及装配
>     - 显示地在Xml文件中注册及装配
>     - 隐式地装配，即自动注册及自动装配
> - 注册：注册配置bean的元数据信息（此时Bean还未被创建）
> - 装配：在容器中，将bean与bean之间组合/协作在一起。
> - 自动装配：容器自动装配bean的协作者。
>     - `no/defalt`：这是默认设置，表示没有自动装配。应使用**显式 bean** 显示进行装配。
>     - `byName`：它根据 bean 的名称注入对象依赖项。它匹配并装配其属性与 XML 文件中由相同名称定义的 bean。
>     - `byType`：它根据类型注入对象依赖项。如果属性的类型与 XML 文件中的一个 bean 名称匹配，则匹配并装配属性。
>     - `constructor`：它通过调用类的构造函数来注入依赖项。它有大量的参数。

[Spring 自动注册及自动装配](http://t.zoukankan.com/huangzejun-p-8183730.html)



### 6. 内部bean与外部bean

​		① 使用场景：只有将bean用作另一个bean的**属性**（<u>has a</u>）时，才能将bean声明为内部bean。基于XML的配置元数据在`<property>`或`<constructor-arg>`中提供了`<bean>`元素的使用。

​		<u>通俗来说怎么理解呢？其实就是代替 **ref属性引用bean的id** 给属性赋值，而直接使用匿名内部bean进行属性赋值</u>。



​		② 具体区别

- 内部bean：内部bean总是**匿名**的，只能在`<property>`或`< constructor-arg>`标签内部被bean的某个属性使用。
- 外部bean：直接在`<beans>`标签内使用`<bean>`标签定义的bean对象叫做外部bean，外部bean可以在`<beans>`中被其它的外部bean多次引用。



​		③ 案例

- POJO类

```java
public class Address {
    private String province;
    private String city;
}

public class People {
    private String name;
    private Integer age;
    private Address address;
}
```



- applicationContext.xml

```xml
<!-- 内部bean出现在property标签中 -->
    <bean id="peopleDiSetUseInnerBeanOfProperty" class="com.lujun61.bean.People">
        <property name="name" value="WYY"/>
        <property name="age" value="18"/>
        <property name="address">
            <bean class="com.lujun61.bean.Address">
                <property name="province" value="HuBei"/>
                <property name="city" value="GongAn"/>
            </bean>
        </property>
    </bean>

    <!-- 内部bean出现在constructor-arg标签中 -->
    <bean id="peopleDiSetUseInnerBeanOfConstruct" class="com.lujun61.bean.People">
        <constructor-arg name="name" value="LKQ"/>
        <constructor-arg name="age" value="18"/>
        <constructor-arg>
            <bean class="com.lujun61.bean.Address">
                <property name="province" value="HuBei"/>
                <property name="city" value="GongAn"/>
            </bean>
        </constructor-arg>
    </bean>
```



### 7. Spring 循环依赖

[Spring是如何解决循环依赖的？](https://blog.51cto.com/u_15281317/2942370)

[年薪50万的一个面试题，看着不难，却刷掉了99%的人！ (qq.com)](https://mp.weixin.qq.com/s/pQaX2-BqFsO3pzPELWIDfQ)

#### 1）什么是循环依赖

​		A 依赖 B，B 又依赖 A，它们之间形成了循环依赖（无论先创建InstanceA还是InstanceB时，都会发生循环依赖！）。或者是 A 依赖 B，B 依赖 C，C 又依赖 A。它们之间的依赖关系如下：

|                图解循环依赖                |
| :----------------------------------------: |
| ![什么是循环依赖](imgs\什么是循环依赖.png) |



#### 2）什么是[三级缓存](https://blog.csdn.net/m0_67401417/article/details/125244712)？对象在三级缓存中的创建流程？

##### ① 三级缓存源代码

```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {

    /* 
    	Cache of singleton objects: bean name to bean instance. 
    	一级缓存
    */
    private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

    /* 
    	Cache of singleton factories: bean name to ObjectFactory. 
    	三级缓存
    */
    private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

    /* 
    	Cache of early singleton objects: bean name to bean instance. 
    	二级缓存
    */
    private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);
}    
```



##### ② 作用分析

- 第一级缓存：也叫单例池，存放已经经历了完整生命周期的Bean对象；
- 第二级缓存：用于存放提前曝光的单例对象的cache，存放原始的 `bean 对象（尚未填充属性）`，用于解决循环依赖；
- 第三级缓存：单例对象工厂的cache，存放 bean的 `工厂对象`，用于解决循环依赖。

注：

​		只有单例的bean会通过三级缓存提前暴露来解决循环依赖的问题，而非单例的bean，每次从容器中获取都是一个新的对象，都会重新创建，所以非单例的bean是没有缓存的，不会将其放到三级缓存中。

​		为了解决二级缓存中AOP生成新对象的问题，Spring就提前AOP，比如在加载b的流程中，如果发送了循环依赖，b依赖了a，就要对a执行AOP，提前获取增强以后的a对象，这样b对象依赖的a对象就是增强以后的a了。

​		二三级缓存就是为了解决循环依赖，且之所以是二三级缓存而不是二级缓存，主要是可以解决循环依赖对象需要提前被aop代理，以及如果没有循环依赖，早期的bean也不会真正暴露，不用提前执行代理过程，也不用重复执行代理过程。



##### ③ 对象在三级缓存中的创建流程

Eg：A依赖B，B依赖A

1、A创建过程中需要B，于是先将A放到三级缓存，去实例化B。

2、B实例化的过程中发现需要A，于是B先查一级缓存寻找A，如果没有，再查二级缓存，如果还没有，再查三级缓存，找到了A，然后把三级缓存里面的这个A放到二级缓存里面，并删除三级缓存里面的A。

3、B顺利初始化完毕，将自己放到一级缓存里面（此时B里面的A依然是创建中的状态）。然后回来接着创建A，此时B已经创建结束，可以直接从一级缓存里面拿到B，去完成A的创建，并将A放到一级缓存。



#### 3）多例和构造器为什么无法解决循环依赖

- 为什么多例Bean不能解决循环依赖？
        	只有单例的bean会通过三级缓存提前暴露来解决循环依赖的问题，而非单例的bean，每次从容器中获取都是一个新的对象，都会重新创建（不走缓存，没有我就新建），所以**多例的bean是没有缓存的**，不会将其放到三级缓存中。

- 为什么Spring不能解决构造器的循环依赖？

    ​		循环依赖的解决是**实例化后**通过三级缓存来解决的，但构造器是在实例化时调用的，此时bean还没有实例化完成。如果此时出现了循环依赖，一二三级缓存并没有Bean实例的任何相关信息，因此当getBean的时候缓存并没有命中，这样就抛出了循环依赖的异常了。总结：**Spring的三级缓存解决的是实例化之后属性赋值的循环依赖，构造器被调用是在实例化之前，所以无法解决构造器的循环依赖！**



#### 4）总结

​		Spring只能解决**setter注入的单例bean之间**的循环依赖。ClassA依赖ClassB，ClassB又依赖ClassA，形成依赖闭环。Spring在创建ClassA对象后，不需要等给属性赋值，直接将其曝光到bean缓存当中。在解析ClassA的属性时，又发现依赖于ClassB，再次去获取ClassB，当解析ClassB的属性时，又发现需要ClassA的属性，但此时的ClassA已经被提前曝光加入了正在创建的bean的缓存中，则无需创建新的的ClassA的实例，直接从缓存中获取即可。从而解决循环依赖问题。



### 8.  bean生命周期中的初始化方法与销毁方法

#### 1）什么是初始化方法、销毁方法

- 初始化方法：在bean对象被**初始化之后**（<u>属性已经被赋值完毕了</u>）执行的方法。是Spring提供给开发者的**对象初始化时机**，让开发者可以在<font color="green"><u>对象初始化时</u></font>进行一些扩展操作。例如：环境准备。
- 销毁方法：在bean对象被**销毁之前**执行指定的销毁方法。是Spring提供给开发者的**对象销毁时机**，让开发者可以在<u><font color="green">对象被销毁之前</font></u>进行一些扩展操作。例如：关闭资源操作。



#### 2） 实现的多种方式->注解方式、实现接口方式、XML配置方式

- 使用**注解**的方式指定初始化方法、销毁方法-----------==最先执行==
    - **@PostConstruct**：注解初始化方法。
    - **@PreDestroy**：注解销毁方法。
- 实现**接口**的方式指定初始化方法、销毁方法------------==次之==
    - **InitializingBean接口**：重写`afterPropertiesSet`方法，这个方法便是初始化方法。
    - **DisposableBean接口**：重写`destroy`方法，这个方法便是销毁方法。

- 使用**XML配置**（bean标签中的属性）的方式指定初始化方法、销毁方法------==最后执行==
    - **init-method**：XML配置初始化方法。
    - **destroy-method**：XML配置销毁方法。


> ​		**<font color="green">注意：初始化方法与销毁方法都不能带有参数；可以是public、protected以及private的；还可以被final修饰并且可以抛出异常</font>**。初始化方法和销毁方法都是定义在**当前对象**中的方法。
>
> ​		它们的**执行时机**不同，仅此而已。
>
> ---
>
>​		Spring虽然可以通过`afterPropertiesSet`完成bean初始化后对这个bean的回调，但是这种方式要求bean实现InitializingBean接口。**<font color="blue">一但bean实现了InitializingBean接口，那么这个bean的代码就和Spring耦合到一起了</font>**。通常情况下不建议bean直接实现InitializingBean，可以使用Spring提供的init-method的功能来执行一个bean子定义的初始化方法。



demo：演示多种方式的执行顺序

```java
public class Bean implements InitializingBean， DisposableBean {

    private String name;
    private Integer age;

    // 无参、set、get、toString

    @PostConstruct
    public void init2() {
        System.out.println("@PostConstruct");
    }

    @PreDestroy
    public void destory2() {
        System.out.println("@PreDestroy");
    }

    // 在xml配置中使用init-method属性指定
    public void init() {
        //System.out.println("初始化成功---name属性为：" + this.name);
        System.out.println("init-method");
    }

    // 在xml配置中使用destory-method属性指定
    public void destory() {
        // 只有在单例模式（singleton）下，destory方法才会生效
        //System.out.println("单例对象即将被销毁---name属性为：" + this.name);
        System.out.println("destory-mothod");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitializingBean");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("DisposableBean");
    }
}

/*                            测试方法                              */
Test {
    ClassPathXmlApplicationContext applicationContext =
        new ClassPathXmlApplicationContext("applicationContext.xml");

    Bean student = (Bean) applicationContext.getBean("studentForLifeCycle2");

    System.out.println(student);   // 使用对象

    //需要使用子类容器调用销毁方法而不是ApplicationContext，因为ApplicationContext没有close()
	// 销毁容器 <===> 销毁对象
    applicationContext.close();
}

/*
    无参
    setName方法执行了！！！！！
    setAge方法执行了！！！！！
    
    @PostConstruct
    InitializingBean
    init-method
    
    Bean{name='LJ'， age=18}      // 使用对象
    
    @PreDestroy 
    DisposableBean
    destory-mothod
*/
```



|                  SpringBean的生命周期-初版                   |
| :----------------------------------------------------------: |
| ![SpringBean的生命周期-初版](imgs\SpringBean的生命周期-初版.png) |



### 9. Spring中Bean的多种创建方式

> ​		Spring为Bean提供了多种创建方式，通常包括4种方式（也就是说在Spring中为Bean对象的创建准备了多种方案），目的是：**使Bean的创建更加灵活**。
>
> - 灵活
>     - 不局限于在主配置文件中通过标签配置创建对象
>     - 在创建复杂对象时，如果仅仅通过标签配置，将会显得格外繁琐、麻烦。此时，使用其它的Bean对象创建方式，便显得格外可行了。



#### 1）通过构造方法实例化

- 准备POJO类

```java
public class User {
    public User() {
        System.out.println("User类的无参数构造方法执行。");
    }
}
```



- Spring主配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userBean" class="com.lujun61.bean.User"/>
	
</beans>
```



#### 2）通过简单工厂模式实例化

> ​		简单工厂（静态工厂）未被实例化。直接指定其中创建Bean对象的<u>静态方法</u>即可。

- 准备POJO类

```java
public class Vip {
}
```



- 创建**简单工厂模式**中的工厂类
    - 工厂类中创建一个用于获取bean的静态方法

```java
public class VipFactory {
    public static Vip get(){
        return new Vip();
    }
}
```



- 在Spring主配置文件中使用创建的`工厂Bean`创建对象

> ​		注意：由于是**简单工厂模式（静态工厂模式）**，所以VipFactory工厂类并未被实例化！
>
> ```java
> Class.forname("com.lujun61.bean.VipFactory").getMethed("get").invoke(null);
> ```

```xml
<!-- 表示使用`VipFactory`中的使用`factory-method`指定的工厂方法创建`Bean对象` -->
<bean id="vipBean" class="com.lujun61.bean.VipFactory" factory-method="get"/>
```



#### 3）通过factory-bean实例化

> ​		这种方式本质上是：通过**工厂模式**进行实例化。
>
> ​		所以，需要先创建`工厂bean`对象，用来创建bean对象。再利用这个工厂bean对象创建bean对象。



- 准备POJO类

```java
public class Order {
}
```



- 创建**工厂模式**中的工厂类
    - 这个工厂类也是一个Bean
    - 在主配置文件中需要先创建这个工厂Bean
    - 再使用工厂Bean中的方法创建对象

```xml
<!-- 创建`工厂Bean对象` -->
<bean id="orderFactory" class="com.lujun61.bean.OrderFactory"/>

<!-- 根据`工厂Bean对象`中的方法来创建一个`Bean对象` -->
<bean id="orderBean" factory-bean="orderFactory" factory-method="get"/>
```



#### 4）通过FactoryBean接口实例化

> ​		以上的第三种方式中，`factory-bean`是我们自定义的，`factory-method`也是我们自己定义的。
>
> ​		在Spring中，当你编写的类直接实现`FactoryBean`接口之后，`factory-bean`就不需要指定了，`factory-method`也不需要指定了。
>
> ​		**factory-bean会自动指向实现FactoryBean接口的类，factory-method会自动指向getObject()方法**。
>
> - FactoryBean接口源码
>
> ```java
> public interface FactoryBean<T> {
>  	String OBJECT_TYPE_ATTRIBUTE = "factoryBeanObjectType";
>     
>     @Nullable
> 	T getObject() throws Exception;
>     
>     @Nullable
> 	Class<?> getObjectType();
> 
>     default boolean isSingleton() {
> 		return true;
> 	}
> }    
> ```
>



- 准备POJO类

```java
public class Person {
}
```



- 编写`FactoryBean`接口实现类

```java
public class PersonFactoryBean implements FactoryBean<Person> {

    @Override
    public Person getObject() throws Exception {
        return new Person();
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        // true表示单例
        // false表示原型
        return true;
    }
}
```



- 在主配置文件中使用`FactoryBean`创建`Bean对象`

```xml
<bean id="personBean" class="com.lujun61.bean.PersonFactoryBean"/>
```



### 10. FactoryBean与BeanFactory的区别

[(33条消息) BeanFactory和FactoryBean的区别_洋洋洋coding的博客-CSDN博客_beanfactory和factorybean的区别](https://blog.csdn.net/weixin_56311692/article/details/125782919)

#### 1）BeanFactory

> ​		**它是一个工厂**。是Spring IoC容器的顶级根接口，BeanFactory被翻译为`Bean工厂`，在Spring的IoC容器中，`Bean工厂`负责按照一个固定的流程（Bean的生命周期）创建Bean对象，并管理Bean对象。



#### 2）FactoryBean

> ​		**它是一个Bean**。是一个能够**辅助Spring**实例化其它Bean对象的一个特殊的Bean，是bean的加工工厂，是对已知Bean的加工。它并不要求用户使用一个固定的模式创建一个Bean对象，而是提供给用户一个灵活的创建方式。

在Spring中，Bean可以分为两类：

- 第一类：普通Bean
- 第二类：工厂Bean。**记住：工厂Bean也是一种Bean，只不过这种Bean比较特殊，它可以辅助Spring实例化其它Bean对象，是Spring提供的创建Bean对象的一种方式。这在创建复杂Bean对象时是非常有用的，因为创建复杂Bean对象时，使用<bean>标签的灵活性十分受限。**

> ​				注意：工厂 ≠ Bean



### 11. 事务中@Transactional常见失效场景

Spring生成代理的方式有两种：

- 基于接口的JDK动态代理，要求目标代理类需要实现一个接口才能被代理
- 基于实现目标类子类的CGLIB代理

> ​		Spring在2.0之前，目标类如果实现了接口，则使用JDK动态代理方式，否则通过CGLIB子类的方式生成代理。而在2.0版本之后，如果不在配置文件中显示的指定`spring.aop.proxy-target-class`的值，默认情况下生成代理的方式皆为CGLIB。

[(28条消息) 一口气怼完12种@Transactional的失效场景_普通网友的博客-CSDN博客_transactional不生效的场景](https://blog.csdn.net/superjava_/article/details/122261559)

[(28条消息) 使用@Transactional需要注意的问题： @Transactional 只能应用到 public 方法 ；AOP自调用问题_Berwinnnnn的博客-CSDN博客](https://blog.csdn.net/Manchester__united/article/details/114379055)

#### 1）非public修饰的方法

[(31条消息) 为什么spring的事务注解@Transaction只能用在public方法上_南波兔不写巴哥的博客-CSDN博客_@transaction private](https://blog.csdn.net/qq_34687559/article/details/117636856)

​		非 public 修饰的方法上，即使加了@Transactional，事务依然不会生效。这是因为@Transactional使用的是`Spring AOP`实现的，而 Spring AOP是通过`动态代理`实现的，而@Transactional在生成代理时会进行判断：**如果方法为非 public修饰的方法，则不生成代理对象**，这样也就没办法自动执行事务了。它的部分实现源码如下：

- `AbstractFallbackTransactionAttributeSource`类：

|             部分实现源码---判断是否为public方法              |
| :----------------------------------------------------------: |
| ![image-20230129215247197](imgs\image-20230129215247197.png) |



#### 2）被final、static关键字修饰的类或方法

​		CGLIB是通过生成目标类子类的方式生成代理类的，被final、static修饰后，无法继承父类与父类的方法。



####  3）@Transactional方法中使用try/catch处理异常

​		@Transactional的执行逻辑是：**@Transactional会在方法执行前，自动开启事务；在方法成功执行完后，会自动提交事务；如果方法在执行期间，出现了异常，那么它会自动回滚事务**。

​		然而如果在方法中自行添加了 try/catch之后，事务就不会自动回滚了，这和@Transactional注解的实现有关：**反射调用目标方法的时候，使用try/catch捕获异常的方式进行事务的操作，所以如果在方法中再使用try/catch，就会将异常“吃掉”。若没有再把异常向调用层抛出，事务就不会回滚**。它的部分实现源码如下：

|       源码-调用目标方法时使用try/catch进行事务逻辑操作       |
| :----------------------------------------------------------: |
| ![image-20230129220121544](imgs\image-20230129220121544.png) |



#### 4）显示|隐式使用`this`对象调用类内部的@Transactional方法

​		上述代码在添加用户之后即使遇到了异常，程序也没有执行回滚，这是因为**@Transactional是基于 `Spring AOP`实现的，而 Spring AOP又是基于`动态代理`实现的，而当调用类内部的方法时，不是通过代理对象完成的，而是通过`this对象`实现的，这样就绕过了代理对象，事务也就失效了**。

|                             案例                             |
| :----------------------------------------------------------: |
| ![image-20230129222312970](imgs\image-20230129222312970.png) |

注意：`return save(userInfo);`也就是`return this.save(userInfo);`



#### 5）数据库存储引擎不支持事务

​		例如：MySQL数据库，必须设置数据库存储引擎为InnoDB。





## To Be Continue~



Q：CGLIB动态代理既然有无接口都可以使用。那为什么还会存在JDK的动态代理。

A：像MyBatis的Mapper接口代理模式，就必须使用JDK的动态代理了。因为不存在目标类，也就无法使用CGLIB进行继承操作。使用JDK动态代理之后，代理方法直接在InvocationHandle的invoke方法中执行所有业务逻辑操作即可。



- 有关ComponentScan注解

[Spring深入理解之ComponentScan - 简书 (jianshu.com)](https://www.jianshu.com/p/bccea567eb4c)



- 有关Configuration注解

[Spring @Configuration和@Component的区别_java_脚本之家 (jb51.net)](https://www.jb51.net/article/153430.htm)



- 有关单例

[(66条消息) 单例Bean，单例模式，单例池之间的联系与底层原理_本来写bug却写成了程序的博客-CSDN博客](https://blog.csdn.net/m0_57560984/article/details/120442474)

