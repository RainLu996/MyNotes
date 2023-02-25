# SpringSSM整合开发

具体项目实现步骤：
    1、创建数据库表文件
    2、新建maven--javaWeb项目
    3、加入依赖：
        SpringMVC、Spring、MyBatis、jackson、mySql驱动、druid数据库连接池、jsp、servlet依赖
    4、修改web.xml
        1）注册DispatcherServlet
            目的：创建SpringMVC容器对象->装载Controller类对象，一个Servelt，用来接收用户请求
        2）注册用来创建Spring容器的监听器：ContextLoaderListener
            目的：在web运行期间，只创建一次Spring容器对象
        3）注册【字符集过滤器】：CharacterEncodingFilter
            目的：解决请求参数乱码的问题
    5、创建包：
        controller service dao 实体类包
    6、写SpringMVC Spring MyBatis的配置文件
    7、写代码：dao接口与配对的mapper文件；service接口及其实现类；controller类；实体类
    8、写用户交互页面



- POM依赖

```xml
<dependencies>
    <!--servlet-->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    <!-- jsp 依赖 -->
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2.1-b03</version>
        <scope>provided</scope>
    </dependency>
    <!--springmvc-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <!--事务的-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <!--aspectj 依赖-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <!--jackson-->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
        <version>2.9.0</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.0</version>
    </dependency>
    <!--mybatis 和 spring 整合的-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>1.3.1</version>
    </dependency>
    <!--mybatis-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.1</version>
    </dependency>
    <!--mysql 驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.9</version>
    </dependency>
    <!--druid-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.12</version>
    </dependency>
</dependencies>

<build>
    <resources>
        <resource>
            <directory>src/main/java</directory><!--java目录-->
            <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
                <include>**/*.properties</include>
                <include>**/*.yml</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
         <resource>
            <directory>src/main/resources</directory><!--resources目录-->
            <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
                <include>**/*.properties</include>
                <include>**/*.yml</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```



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
        <package name="com.lujun61.entity"/>
    </typeAliases>

    <mappers>
        <package name="com.lujun61.dao"/>
    </mappers>
</configuration>
```



- applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:conf/jdbc.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:conf/myBatis.xml"/>
    </bean>

    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <property name="basePackage" value="com.lujun61.dao"/>
    </bean>

    <!--扫描创建service、dao层对象-->
    <context:component-scan base-package="com.lujun61.service"/>
</beans>
```





- dispatcherServlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--扫描创建控制层对象-->
    <context:component-scan base-package="com.lujun61.controller"/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <mvc:annotation-driven/>
</beans>
```



- web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--给创建的ServletContext容器设置参数：Spring主配置文件路径-->
    <context-param>
        <!-- contextConfigLocation：表示配置文件的路径 -->
        <param-name>contextConfigLocation</param-name>
        <!-- 自定义配置文件的路径 -->
        <param-value>classpath:conf/applicationContext.xml</param-value>
    </context-param>
    <!--监听ServletContext创建，在其中创建Spring容器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>

        <!-- 设置项目中使用的字符编码 -->
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>

        <!-- 强制【请求对象HttpServletRequest】去使用encoding配置的字符编码方式 -->
        <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>

        <!-- 强制【响应对象HttpServletResponse】去使用encoding配置的字符编码方式 -->
        <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <!-- /*：强制所有【请求】先通过过滤器处理 -->
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--注册DispatcherServlet：需要在容器启动时创建-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:conf/dispatcherServlet.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
</web-app>
```





- jdbc.properties

```properties
jdbc.url=jdbc:mysql://localhost:3306/student
jdbc.username=
jdbc.password=
```



