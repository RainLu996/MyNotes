# Spring配置相关

## 一、Spring集成MyBatis

### 1. 步骤

```txt
Spring集成MyBatis的步骤：

    1、加入依赖
        1）Spring框架依赖
        2）MyBatis框架依赖
        3）MySql驱动依赖
        4）Spring处理事务的依赖
        5）Spring集成MyBatis的依赖：
            用来在Spring项目中创建MyBatis的SqlSessionFactory和dao对象
        6）junit测试单元依赖
    2、创建MyBatis主配置文件
    3、创建Spring配置文件：
        将【与MyBatis相关的对象】交给Spring管理创建---使用到的技术是：IOC
            1）数据源(DataSource)
            2）SqlSessionFactory
            3）通过代理生成的Dao对象-使用MapperScanner扫描创建
            
```



### 2. 配置信息

- myBatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    
    <typeAliases>
        <!-- 起别名：把包中的所有类都起别名（类名首字母小写） -->
        <package name="com.lujun61.bean"/>
    </typeAliases>
    
    
    <!-- 原本的<environments>配置被移除了。因为我们需要将<environments>中所用到的事务管理、数据源，统统交给Spring管理 -->

    <mappers>
        <package name="com.lujun61.dao"/>
    </mappers>
</configuration>
```



- applicationConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 把数据库的配置信息写在一个独立的文件当中 -->
    <context:property-placeholder location="classpath:jdbc.properties" />

    <!-- 声明数据源DataSource：作用是->连接数据库的 -->
    <bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource"
          init-method="init" destroy-method="close">
        <!-- set注入，给DruidDataSource提供连接数据库的信息 -->
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
        <property name="maxActive" value="${jdbc.maxActive}"/>
    </bean>

    <!-- 声明的是myBatis中提供的SqlSessionFactoryBean类，这个类的内部创建了SqlSessionFactory -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- set注入，把数据库连接池赋值给了dataSource属性 -->
        <property name="dataSource" ref="myDataSource" />

        <!-- 声明myBatis主配置文件的位置
                configLocation属性的类型是：Resource，用来读取配置文件
                它的赋值是特殊的！！！使用【value】
         -->
        <!-- 在Spring的配置文件中指定其它文件的路径，需要使用classpath -->
        <property name="configLocation" value="classpath:myBatis.xml" />
    </bean>

    <!-- 创建dao对象，使用SqlSession的getMapper(StudentDao.class)生成每个dao接口的代理对象
            MapperScannerConfigurer：在内部调用getMapper()
     -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 指定SqlSessionFactory对象的id -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>

        <!-- 指定包名，包名是dao接口所在的包名
                 MapperScannerConfigurer会扫描这个包中的所有接口，
                 并把【每个包中的接口】都执行一次【getMapper()】方法，得到每个接口的dao对象。
                 【再将创建好的dao对象放入到Spring容器中】
                 这里自动创建的dao接口对象的名称为：接口首字母小写
        -->
        <property name="basePackage" value="com.lujun61.dao"/>
        <!-- 多个包的写法：用逗号分隔
        <property name="basePackage" value="com.lujun61.dao1,com.lujun61.dao2"/>-->
    </bean>
</beans>
```



























































