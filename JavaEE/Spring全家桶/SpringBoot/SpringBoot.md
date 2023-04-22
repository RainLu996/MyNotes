# SpringBoot

## 引入

### 1. Spring、SpringBoot、SpringCloud的关系

​		Spring最核心的两大核心功能 `IoC` 和 `AOP`成就了 Spring。Spring在这两大核心的功能上不断的发展，才有了 <u>Spring事务</u>、<u>Spring MVC</u>等一系列伟大的产品，最终成就了Spring帝国。

​		到了后期，Spring几乎可以解决企业开发中的所有问题。**Spring Boot**是在强大的 Spring帝国生态基础上面发展而来。<u>发明 Spring Boot不是为了取代Spring，是为了让人们更容易的使用 Spring</u>。**Spring Cloud**是一系列框架的有序集合，它利用 Spring Boot的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 Spring Boot的开发风格做到一键启动和部署。<u>Spring Cloud是为了解决微服务架构中服务治理而提供的**一系列功能**（不是单指一个框架）的开发框架</u>，并且**Spring Cloud是完全基于 Spring Boot而开发**，Spring Cloud利用 Spring Boot的特性整合了开源行业中优秀的组件,整体对外提供了一套在`微服务架构`中服务治理的解决方案。用一组不太合理的包含关系来表达它们之间的关系。



- 助理解：它们的包含关系

> ​		Spring IoC/AOP > Spring > Spring Boot > Spring Cloud



### 2. 为什么要使用SpringBoot

1. 遵循[^约定优于配置]的原则 ； 常用的框架和第三方库都已经自动装配好了。只需要根据实际需要，简单配置一下就可以使用了。
2. 提供starter起步依赖，我们只需要选择所需要的功能，由SpringBoot帮我们管理一系列相关依赖。
3. 简化配置，无需XML ；提高开发效率，方便使用。
4. 内嵌Servlet容器。使得SpringBoot应用程序可以作为一个独立的Java应用程序运行。
5. 与主流框架集成简单。



### 3. 如何理解starter起步依赖

> ​		Starter可以理解为启动器，它包含了一系列可以集成到应用里面的依赖包。我们可以一站式集成 Spring及其它技术，而不需要到处找示例代码和依赖包。

[（四）SpringBoot起步依赖Starters详解 - 爱码网 (likecs.com)](https://www.likecs.com/show-305996858.html)



- 扩展：starter依赖命名模式
  - 所有官方starters遵循相似的命名模式： `spring-boot-starter-\*`
  - 第三方starters不应该以 spring-boot 开头，因为它跟Spring Boot官方artifacts冲突。例如：一个acme的第三方starter通常命名为 `acme-spring-boot-starter` 。



### 4. starter工作原理

​		Spring Boot在启动的时候，按照约定去读取 `Spring Boot Starter`的配置信息，再根据配置信息对资源进行初始化，并注入到 Spring容器中。这样 Spring Boot 启动完毕后，就已经准备好了一切资源，在使用过程中直接注入所需的Bean对象即可。



**细致一点来说：**

- SpringBoot在启动时会去依赖的Starter包中寻找 `resources/META-INF/spring.factories`文件，然后根据文件中配置的Jar包去扫描项目中所依赖的Jar包。
- 根据 `spring.factories`配置加载 `AutoConfigure类`
- 根据`@Conditional`注解的条件，进行自动配置并将Bean注入 `SpringContext容器`



<hr>

[^约定优于配置]:约定优于配置（convention over configuration），也称作按约定编程，是一种软件[设计范式](https://baike.baidu.com/item/设计范式?fromModule=lemma_inlink)，旨在减少软件开发人员需做决定（自行手动配置）的数量。追求简单的同时，而又不失灵活性。



## 第一章 SpringBoot入门 

### 1. SpringBoot中自动装配相关注解

#### 1）@Import

[(32条消息) @Import注解_ZmyCoder的博客-CSDN博客_@import注解](https://blog.csdn.net/a14654/article/details/114398753)

​		自动使用**无参构造函数**创建实例注册到 IOC 容器中。所创建的实例在 IOC 容器中**默认的id名为类的全限定名**。（@Import中导入的类可以不被Spring容器管理）

```java
@Import({User.class, DBHelper.class})
@Configuration
public class MyConfig {
}
```



#### 2）@Conditional

​		**条件装配注解**。作用是：如果满足Conditional系列注解所指定的条件，则进行组件注入。

例如：

- @ConditionalOnBean：当容器中存在所指定的bean的时候，进行组件注入
- @ConditionalOnMissingBean：当容器中不存在所指定的bean的时候，进行组件注入



## 第二章 Spring Boot

### 1. 介绍

SpringBoot是Spring中的一个成员， 可以简化Spring，SpringMVC的使用。 他的核心还是IOC容器。

特点：

- Create stand-alone Spring applications

   创建spring应用

  

- Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)

  内嵌的tomcat， jetty ， Undertow 

  

- Provide opinionated 'starter' dependencies to simplify your build configuration

  提供了starter起步依赖，简化应用的配置。   

  比如使用MyBatis框架， 需要在Spring项目中，配置MyBatis的对象 SqlSessionFactory， Dao的代理对象。

  而在SpringBoot项目中，只需要在pom.xml里面，加入一个 mybatis-spring-boot-starter依赖即可。

  

- Automatically configure Spring and 3rd party libraries whenever possible

  尽可能去配置spring和第三方库。叫做自动配置（就是把spring中的，第三方库中的对象都创建好，放到容器中， 开发人员可以直接使用）

  

- Provide production-ready features such as metrics, health checks, and externalized configuration

   提供了健康检查， 统计，外部化配置

  

- Absolutely no code generation and no requirement for XML configuration

  不用生成代码， 不用使用xml，做配置



### 2. 创建Spring Boot项目

- 阿里云镜像：https://start.aliyun.com





### 3.  @SpringBootApplication注解

- @SpringBootApplication是一个<u>复合注解</u>
  - @SpringBootConfiguration
  - @EnableAutoConfiguration
  - @ComponentScan



1. @SpringBootConfiguration

   ​		说明：使用`@SpringBootConfiguration`注解标注的类，可以作为**配置文件**使用，可以使用Bean声明对象，注入到容器中。

```java
@Configuration
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```



2.@EnableAutoConfiguration

​		启用自动配置， 把Java对象配置好，注入到Spring容器中。例如可以把mybatis的对象创建好，放入到容器中。另外，通过配置，可以<u>关闭对某个属性的自动配置</u>：

```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
```



3.@ComponentScan

​		@ComponentScan 扫描器，找到注解，根据注解的功能创建对象，给属性赋值等等。
默认扫描的包： **@ComponentScan所在的类（一般来说，就是主启动类）所在的包和子包**。

- 我们可以通过属性配置来更改扫描的路径：

```java
@SpringBootApplication(scanBasePackages="TargetPath……")
```



### 4. SpringBoot的配置文件

配置文件名称： application

扩展名有： properties( k=v) ;  yml ( k: v)

使用application.properties,   application.yml

> ​		如果yml与properties并存，properties的优先级将更高。
>
> ​	    但从 SpringBoot2.4 开始， yml 配置文件的优先级将更高。



例1：application.properties设置 端口和上下文

```properties
#设置端口号
server.port=8082
#设置访问应用上下文路径， contextpath
server.servlet.context-path=/myboot
```



例2： application.yml

```yaml
server:
  port: 8083
  servlet:
    context-path: /myboot2
```



#### yml配置文件用法

##### ① 简介

​		YAML 是 "YAML Ain't Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。 非常适合用来做以数据为中心的配置文件。



##### ② 基本用法

- key: value；k、v之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格（一般使用两个空格表示缩进）
- `#`表示注释
- 字符串无需加引号。如果要加，双引号与单引号会存在区别
  - 单引号`''`：加上单引号后，字符串中的转移字符不会生效，仍然会被当做普通字符串输出。即：`\\`。
  - 双引号`""`：加上双引号后，字符串中的转移字符会生效。即：`\`。



##### ③ yml中的数据类型

- 字面量：单个的、不可再分的值。date、boolean、string、number、null……

```yaml
k: v
```



- 对象：键值对的集合。map、hash、set、object 

```yaml
# 行内写法：  
k: {k1:v1,k2:v2,k3:v3}  # {}中的k、v不空格也没关系。
# 或者
k: 
  k1: v1
  k2: v2
  k3: v3
```



- 数组：一组按次序排列的值。array、list、queue

```yaml
# 行内写法：  
k: [v1,v2,v3]
# 或者
k:
 - v1
 - v2
 - v3
```



##### ④ 示例

- 实体类

```java
@Data
public class Person {
	
	private String userName;
	private Boolean boss;
	private Date birth;
	private Integer age;
	private Pet pet;
	private String[] interests;
	private List<String> animal;
	private Map<String, Object> score;
	private Set<Double> salarys;
	private Map<String, List<Pet>> allPets;
}

@Data
public class Pet {
	private String name;
	private Double weight;
}
```



- yml配置文件

```yaml
# yaml表示以上对象
person:
  userName: zhangsan
  boss: false
  birth: 2022/10/26 20:12:33
  age: 18
  pet: 
    name: RainLu
    weight: 23.4
  interests: [篮球,游泳]
  animal: 
    - jerry
    - mario
  score:
    english: 
      first: 30
      second: 40
      third: 50
    math: [131,140,148]
    chinese: {first: 128,second: 136}
  salarys: [3999,4998.98,5998.99]
  allPets:
    sick:
      - {name: tom}
      - {name: jerry,weight: 47}
      - name: wangcai  # 一个-，就代表一个元素。可以是单值形式，也可以是k、v形式
        weight: 38
    health: [{name: mario,weight: 47}]
```





### 5. 多环境配置

有开发环境， 测试环境， 上线的环境。

每个环境有不同的配置信息， 例如端口， 上下文件， 数据库url，用户名，密码等等



使用多环境配置文件，可以方便的切换不同的配置。

使用方式： 创建多个配置文件， 名称规则： `application-环境名称.properties(yml)`



创建开发环境的配置文件： application-dev.properties(  application-dev.yml )

创建测试者使用的配置： application-test.properties

- **application**.properties     or    **application**.yml

```properties
# 启用后缀为dev的配置文件
spring.profiles.active=dev
```



### 6. 使用jsp

**SpringBoot不推荐使用jsp ，而是使用模板技术代替jsp。**



使用jsp需要配置：

1） 加入一个处理jsp的依赖。 负责编译jsp文件

```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>
```



2) 如果需要使用servlet， jsp，jstl的功能

```xml
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>jstl</artifactId>
</dependency>

<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
</dependency>

<dependency>
<groupId>javax.servlet.jsp</groupId>
	<artifactId>javax.servlet.jsp-api</artifactId>
	<version>2.3.1</version>
</dependency>

```



3) 创建一个存放jsp的目录，一般叫做webapp

​    index.jsp



4) 需要在pom.xml指定jsp文件<u>编译后</u>的存放目录。

  `META-INF/resources`

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.*</include>
            </includes>
        </resource>


        <!--
            SpringBoot 要求 jsp 文件必须编译到指定的META-INF/resources 目录下才能访问，
            否则访问不到。【官方已经建议使用模版技术（后面课程会单独讲解模版技术）】
        -->
        <resource>
            <!-- jsp文件存在的目录 -->
            <directory>src/main/webapp</directory>

            <!--指定要把哪些文件编译进去，**表示 webapp 目录及子目录，*.*表示所有文件-->
            <includes>
                <include>**/*.*</include>
            </includes>

            <!-- 指定JSP编译后的存放目录 -->
            <targetPath>META-INF/resources</targetPath>
        </resource>
    </resources>

    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.lujun61.Application</mainClass>
            </configuration>
        </plugin>

    </plugins>
</build>
```



5）创建Controller， 访问jsp

……



6）在application.propertis文件中配置视图解析器

```yaml
spring:
  mvc:
    view:  #配置视图解析器
      prefix: /  # / 相当于 src/main/webapp 目录
      suffix: .jsp
```

<hr>

扩展知识：

> ​		`META-INF`文件夹相当于一个信息包，其中的文件和目录获得Java 2平台的认可与解释，用来配置应用程序、扩展程序、类加载器和服务。其中的 `MANIFEST.MF` 文件，在用jar打包时自动生成。 
> ​		WEB-INF是Java的WEB应用的安全目录。所谓安全就是客户端无法访问，只有服务端可以访问的目录。如果想在页面中直接访问其中的文件，必须通过`web.xml`文件对要访问的文件进行相应映射才能访问。
> ​		JAR 文件与 ZIP 文件唯一的区别就是**在 JAR 文件的内容中，包含了一个 META-INF/MANIFEST.MF 文件**，这个文件是在生成 JAR 文件的时候自动创建的。
>
> ​		**MANIFEST.MF**：这个 MANIFEST文件定义了与扩展和包相关的数据。单词“manifest”的意思是“显示”。



### 7. 使用容器

在SpringBoot中，获取容器，并从容器中获取对象。

​		SpringApplication.run(Application.class, args); 的返回值类型为`ConfigurableApplicationContext`。是`ApplicationContext`的子接口。通过获取`run`方法的返回值，获取SpringBoot项目中的容器对象。

```java
public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
        return run(new Class[]{primarySource}, args);
}

// ConfigurableApplicationContext接口：是ApplicationContext的子接口
public interface ConfigurableApplicationContext extends ApplicationContext, ……
```



### 8. ComnandLineRunner 接口 & ApplcationRunner接口

​		如果开发中需要**在容器启动后执行一些内容**。比如读取配置文件，数据库连接之类的。SpringBoot 给我们提供了两个接口来帮助我们实现这种需求。

​		这两个接口分别为 `CommandLineRunner` 和 `ApplicationRunner`。这两个接口中都有一个 run 方法，我们只需要实现这个方法即可。**在容器对象创建好后， run（）方法会自动执行。**

​		这两个接口的不同之处在于： <u>ApplicationRunner 中 run 方 法 的 参 数 为 ApplicationArguments ， 而 CommandLineRunner 接口中 run 方法的参数为 String 数组</u>。

```java
@FunctionalInterface
public interface CommandLineRunner {
    void run(String... args) throws Exception;
}

// =====================================================

@FunctionalInterface
public interface ApplicationRunner {
    void run(ApplicationArguments args) throws Exception;
}
```





### 9. 使用AOP

- [SpringBoot AOP切面实现](https://blog.csdn.net/wangzhongshun/article/details/124443909)





## 第三章  SpringBoot和Web组件

> ​	配置技巧：
>
> - 配置JavaWeb相关的配置项时，要注意加上@Bean；没有接口可实现；
>   返回值的命名规则记忆---> `相关技术名+RegistrationBean`
>
> - 配置SpringMVC相关的配置项时，不需要加上@Bean；
>   只需要实现`WebMvcConfigurer`接口，实现对应的方法即可。

### 1. 拦截器

> ​		拦截器是SpringMVC中的一种对象，能拦截对Controller的请求。前提是这个请求需要被`DispatcherServlet`所接收。
>
> ​		在SpringBoot框架中除了有系统自带的拦截器， 还可以自定义拦截器。实现对请求预先处理。



实现自定义拦截器：

- 在SpringMVC中自定义拦截器

1. 创建拦截器类实现SpringMVC框架的HandlerInterceptor接口

   ```java
public int	erface HandlerInterceptor {
       default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           return true;
       }

       default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
       }

       default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
       }
}
   ```



2.需在SpringMVC的配置文件中，声明拦截器

```xml
<mvc:interceptors>
	<mvc:interceptor>
    	<mvc:path="url" />
        <bean class="拦截器类全限定名称"/>
    </mvc:interceptor>
</mvc:interceptors>
```



- 在SpringBoot中自定义拦截器

1. 创建拦截器  

……

2. 注册拦截器

```java
/** 注册拦截器
 *
 * 在SpringBoot中，不存在配置文件，无法将拦截器信息通过配置文件进行注册。
 * 所以要想将拦截器对象注入到容器之中，必须通过实现WebMvcConfigurer接口来完成。
 */
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        HandlerInterceptor interceptor = new LoginInterceptor();

        /**
         * 拦截器与过滤器中的 /* 和 /** 有什么区别
         *
         * /* 是拦截所有的文件夹，不包含子文件夹
         * /** 是拦截所有的文件夹及里面的子文件夹
         */

        //指定拦截的请求地址
        String[] pathPatternArray = {"/user/**"};
        //指定不去拦截的地址
        String[] excludePathPatternArray = {"/user/login"};
        registry.addInterceptor(interceptor)
                .addPathPatterns(pathPatternArray).excludePathPatterns(excludePathPatternArray);
    }
}
```



### 2. Servlet

在SpringBoot框架中使用Servlet对象。

使用步骤：

1. 创建Servlet类。 创建类继承HttpServlet
2. 注册Servlet ，让框架能找到Servlet



 例子：

1.创建自定义Servlet

```java
//创建Servlet类
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       //使用HttpServletResponse输出数据，应答结果
        resp.setContentType("text/html;charset=utf-8");
        PrintWriter out  = resp.getWriter();
        out.println("===执行的是Servlet==");
        out.flush();
        out.close();
    }
}
```



2. 注册Servlet

```java
@Configuration
public class WebApplictionConfig {

    //定义方法， 注册Servlet对象
    @Bean
    public ServletRegistrationBean servletRegistrationBean(){

        //public ServletRegistrationBean(T servlet, String... urlMappings)
        //第一个参数是 Servlet对象， 第二个是url地址

        //ServletRegistrationBean bean =
                //new ServletRegistrationBean( new MyServlet(),"/myservlet");


        ServletRegistrationBean bean = new ServletRegistrationBean();
        bean.setServlet( new MyServlet());
        bean.addUrlMappings("/login","/test"); // <url-pattern>

        return bean;
    }
}
```



### 3. 过滤器Filter

Filter是Servlet规范中的过滤器，可以处理请求， 对请求的参数， 属性进行调整。 常常在过滤器中处理字符编码

在框架中使用过滤器：

1. 创建自定义过滤器类
2. 注册Filter过滤器对象



例子：

```java
// 自定义过滤器
public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("执行了MyFilter，doFilter ");
        filterChain.doFilter(servletRequest,servletResponse);
    }
}
```



注册Filter

```java
@Configuration
public class WebApplicationConfig {

    @Bean
    public FilterRegistrationBean filterRegistrationBean(){
        FilterRegistrationBean bean  = new FilterRegistrationBean();
        bean.setFilter( new MyFilter());
        bean.addUrlPatterns("/user/*");
        return bean;
    }
}
```



### 4. 字符集过滤器

> ​		**CharacterEncodingFilter**：解决**post请求**中的乱码问题。
>
> ​		在SpringMVC框架中，是在web.xml中注册过滤器。 配置它的属性。 



- 第一种方式：使用自定义的字符集过滤器

使用步骤：

1. 配置字符集过滤器

   ```java
   @Configuration
   public class WebSystemConfig {
   
       //注册Servlet
       @Bean
       public ServletRegistrationBean servletRegistrationBean(){
           MyServlet myServlet = new MyServlet();
           ServletRegistrationBean reg = new ServletRegistrationBean(myServlet,"/myservlet");
           return reg;
       }
   
   
       //注册Filter
       @Bean
       public FilterRegistrationBean filterRegistrationBean(){
           FilterRegistrationBean reg = new FilterRegistrationBean();
   
           //使用框架中的过滤器类
           CharacterEncodingFilter filter  = new CharacterEncodingFilter();
           //指定使用的编码方式
           filter.setEncoding("utf-8");
           //指定request ， response都使用encoding的值
           filter.setForceEncoding(true);
   
           reg.setFilter(filter);
           //指定 过滤的url地址
           reg.addUrlPatterns("/*");
   
   
           return reg;
       }
   }
   ```

2. 修改application.properties文件， 让自定义的过滤器起作用

```properties
#SpringBoot中默认已经配置了CharacterEncodingFilter。 编码默认ISO-8859-1
#设置enabled=false 作用是关闭系统中配置好的过滤器， 使用自定义的CharacterEncodingFilter
server.servlet.encoding.enabled=false
```



- 第二种方式：<u>使用SpringBoot框架自带的字符集过滤器</u>

修改application.properties文件

```properties
server:
  port: 8082
  servlet:
    context-path: /dev
    encoding:
      enabled: true #启用SpringBoot中自定义的 CharacterEncodingFilter 的设置（若为false为使用自定义的过滤器）
      charset: UTF-8   #指定使用的编码方式
      force: true   #强制request，response都使用charset属性的值
```



## 第四章 在SpringBoot中使用MyBatis



使用MyBatis框架操作数据，在SpringBoot框架集成MyBatis。

使用步骤：

1. mybatis起步依赖 ： 完成mybatis对象自动配置， 对象放在容器中

2. pom.xml 指定把src/main/java目录中的xml文件包含到classpath中

3. 创建实体类Student

4. 创建Dao接口 StudentDao , 创建一个查询学生的方法 

5. 创建Dao接口对应的Mapper文件， xml文件， 写sql语句

6. 创建Service层对象， 创建StudentService接口和他的实现类。 去dao对象的方法。完成数据库的操作

7. 创建Controller对象，访问Service。

8. 写application.properties文件

   配置数据库的连接信息。

- 配置文件

```yaml
#指定 mybatis 的配置， 相当于 mybatis 主配置文件的作用
mybatis:
  # 当dao接口与mapper映射文件分开创建时，需要配置mapper映射文件接口的存放位置
  mapper-locations: classpath:mapper/*.xml   
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  #打印日志
```



- 资源插件

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.*</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.*</include>
            </includes>
        </resource>
    </resources>

    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.lujun61.Application</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
```





### 1. 使用@Mapper注解

> **@Mapper注解**：给 MyBatis 映射器标记dao接口。每个dao接口上都需要使用这个注解。
>
> *        作用：通知MyBatis当前接口是dao接口，需要给它创建接口的代理对象
> *        位置：在（dao）接口的上面

```java
/**
 * @Mapper：告诉MyBatis这是dao接口，创建此接口的代理对象。
 *     位置：在类的上面
 */
@Mapper
public interface StudentDao {

    Student selectById(@Param("stuId") Integer id);
}
```



### 2. @Repository 与 @Mapper的区别

- **@Repository 是 Spring 的注解**。用于声明一个 <u>持久层的Bean</u>。**@Repository单独使用没用**。可以这样理解，注解放在接口上本来就没有意义。Spring项目中在mapper接口上写一个`@Repository`注解，只是一个标识而已，要想真正使这个接口被创建代理类，必须使用`@MapperScan`进行扫描。



- **@Mapper是 mybatis 自带的注解。**在Spring程序中，mybatis需要找到对应的mapper，在运行时生成动态代理类，与数据库进行交互，这时需要用到@Mapper注解进行dao接口的标识。可是当我们有很多mapper接口时，就需要写很多@Mappe注解，这样很麻烦。有一种简便的配置方法便是在启动类上使用`@MapperScan`注解。这样可以自动扫描包路径下所有的mapper接口，从而不用再在接口上添加任何注解。



区别：

- 相同点
  - `@Mapper`和`@Repository`都是作用在dao层接口，为其生成代理对象bean，交给Spring 容器管理。


- 不同点
  - **@Mapper不需要配置扫描地址，可以单独使用**。如果有多个mapper文件的话，可以在项目启动类中加入`@MapperScan(“mapper文件所在包”)`。
  - **@Repository不可以单独使用**，否则程序启动时会报错误（<u>找不到创建的dao接口</u>）。所以要想使用此注解，必须配置`@MapperScan`扫描地址。



### 3. 使用@MapperScan

> ​		一般放在主启动类上面。
>
> ​		可以指定多个包：用逗号分隔开即可。如果xml映射文件与dao接口不在同一包下，则需要在配置文件中指定xml映射文件的所在位置。一般都会配置在resources目录中。
>
> ​		如果xml映射文件与dao接口不在同一包下，又不想在配置文件中配置所在路径。可以在`resources`目录下创建一个与dao接口包目录相同的目录，然后将mapper映射文件存放至此目录中。原理就是：**项目编译之后，它俩仍然会存放在一块。**

```java
/** 有多个dao接口时，在每个接口上面加上@Mapper注解是不方便的；为了简化操作，可以在主类之上加上@MapperScan注解
 *
 * @MapperScan： 扫描所有的 mybatis 的 dao 接口
 * 位置：在主类的上面
 * 属性：basePackages（字符串数组）：指定 dao 接口的所在的包名（可以有多个）。
 * 如果dao 接口和 mapper 文件在【同一目录】，直接这样配置就行；如果不在同一目录，就需要在【配置文件】中配置mapper文件所在的位置
 */
@SpringBootApplication
@MapperScan(basePackages = {"com.lujun61.dao", "com.lujun61.dao1", ……})
public class Application {
}
```





### 4. 事务

- Spring框架中的事务：

1） 管理事务的对象： 事务管理器（接口， 接口有很多的实现类）

​      例如：使用Jdbc或mybatis访问数据库，使用的事务管理器：DataSourceTransactionManager

2 ) 声明式事务：  在xml配置文件或者使用注解说明事务控制的内容

​     控制事务： 隔离级别，传播行为， 超时时间

3）事务处理方式：

​      1） Spring框架中的@Transactional

​      2)    aspectj框架可以在xml配置文件中，声明事务控制的内容



​    

- SpringBoot中使用事务： 上面的两种方式都可以。

1）在主启动类上面添加`@EnableTransactionManager`注解，在业务方法上面添加`@Transactional` 注解。加入注解后，方法就拥有事务功能了。`@Transactional`注解的使用与在Spring中一样。

2）在JavaConfig配置文件中利用AOP配置事务：[SpringBoot利用AOP切面开启全局事务管理](https://blog.csdn.net/qq_40065776/article/details/105625272)。





## 第五章 接口架构风格—RESTful

### 5.1 理解接口

#### 1）接口概念

​		狭义的理解：就是控制器中可以接收用户请求的某个方法。
​		应用程序编程接口，简称API（Application Programming Interface），就是软件系统不同组成部分衔接的约定。使得开发人员无需访问源码，或理解内部的具体细节，就能使用到应用程序所提供的功能。



#### 2）接口规范

​		作为一个后端开发者，我们不仅要完成应用程序接口的开发，还要编写接口的说明文档。



> **例如**：
>
> ​		在前后端分离开发的项目中，前后端之间是通过接口来进行请求和响应的，后端向前端提供服务时就要对外暴露⼀个`URL`；URL的设计不能是随意的，需要遵从⼀定的设计规范。





> **对于RESTful来说**：
>
> - 每个URL请求路径都代表服务器上的唯⼀资源
> - 使用不同的请求方式来表示不同的操作
>     - `post`：**添加** 
>     - `get`：**查询** 
>     - `put`：**修改** 
>     - `delete`：**删除**
>     - `option`：**预检**
>
> - 接口请求数据和应答数据均采用JSON格式（或XML） 
>     - 在每个接口方法上添加 `@ResponseBody` 注解将返回的对象格式设置为`JSON`
>     - 在控制器类上使用 `@RestController` 注解声明控制器中的所有方法都采用JSON格式应答数据
> - 前端通过`无状态的HTTP协议`与后端接口进行交互



### 5.2 REST

#### 1）什么是REST

​		REST :  （英文： `Representational State Transfer` , 中文： **表现层状态转移**。全称是 `Resource Representational State Transfer`）通俗来讲就是：**<font color="red">资源在网络中以某种表现形式进行状态转移。</font>**是一种客户端与服务端接口交互时的**架构风格**和**设计理念**，不是标准。

   	优点： 更简洁，更有层次



- **表现层状态转移**：表现层就是视图层， 显示资源的， 通过视图页面，JSP等等显示操作资源的结果。
  - 资源(Resource)：资源，即在网络中传输的数据。
  - 表现层(Representational)：某种表现形式，比如用JSON，XML，JPEG等。
  - 状态(State)：表示资源<u>被操作后</u>发生变化的那个状态。例如：被修改后的状态、被更新后的状态……
  - 转移(Transfer)： <u>资源是可以变化的，并不是固定不变的。</u> 资源能被创建，能被删除……

> ​		一句话概括<font color="green"><u>表现层状态转移</u></font>：对资源的**<font color="blue">不同操作（操作，会让资源拥有不同的<u>状态</u>）</font>**，资源会有不同的**<font color="blue">状态<u>变化</u>（转移）</font>**。



#### 2）REST中的要素

- 资源：使用`URL（名词）`表示唯一的资源。在互联网中， 使用的图片，视频， 文本，网页等等都是资源。

- 操作：使用`HTTP动词`描述对资源的操作。所以请求的本质就是`无状态的HTTP请求`。
  - 查询资源： 通过url找到资源。 -------------------GET
  - 创建资源： 添加资源。------------------------------POST
  - 更新资源：更新资源 ，编辑。---------------------PUT
  - 删除资源： 去除。------------------------------------DELETE
  
  

#### 3）总结

> ​		在遵循RESTful架构风格的项目中，资源使用URL表示，通过名词表示资源；通过HTTP动作操作资源。使用“ / " 分隔对资源的信息。





#### 4）一句话说明REST

​		使用URL定位资源 ，使用HTTP动作操作资源。



#### 5）相关注解

  @PathVariable :  从url中获取数据

  @GetMapping:    支持的get请求方式，  等同于 @RequestMapping( method=RequestMethod.GET)

  @PostMapping:  支持post请求方式 ，等同于 @RequestMapping( method=RequestMethod.POST)

  @PutMapping:  支持put请求方式，  等同于 @RequestMapping( method=RequestMethod.PUT)

   @DeleteMapping: 支持delete请求方式，  等同于 @RequestMapping( method=RequestMethod.DELETE)

  

  @RestController:  复合注解， 是@Controller 和@ResponseBody组合。

在类的上面使用@RestController ， 表示当前类者的所有方法都加入了 @ResponseBody



### 5.3 在form表单或者ajax中，支持put，delete请求

在SpringMVC中 有一个过滤器， 支持**post请求转为put ,delete**。



过滤器： `org.springframework.web.filter.HiddenHttpMethodFilter`

作用： **把请求中的post请求转为 put ， delete**



实现步骤：

1. application.properties(yml) : 开启使用 `HiddenHttpMethodFilter` 过滤器
2. 在请求页面中，包含 `_method`参数<u>（name属性）</u>， 他的值是 `put`， `delete` <u>（value属性）</u> ，  发起这个请求必须使用`post方式`。

- 开启过滤器配置

```yaml
spring:
  mvc:    
    hiddenmethod:
      filter:
      #启用 HiddenHttpMethodFilter 这个过滤器， 支持 post 请求转为 put，delete
        enabled: true 
```



- ### 示例

- GET：查询资源  --  sql select

```html
<form action="http://localhost:8080/myboot/student/1" method="get">
</form>
```

-  POST：创建资源  -- sql insert

```html
<form action="http://localhost:8080/myboot/student" method="post">
    姓名：<input type="text" name="name" />
    年龄：<input type="text" name="age" />
</form>
```

- PUT：更新资源  --  sql  update

```html
<form action="http://localhost:8080/myboot/student/1" method="post">
    姓名：<input type="text" name="name" />
    年龄：<input type="text" name="age" />
    <input type="hidden" name="_method" value="PUT" />
</form>
```



- DELETE：删除资源  -- sql delete

```html
<form action="http://localhost:8080/myboot/student/1" method="post">
    <input type="hidden" name="_method" value="DELETE" />
</form>
```



 需要的分页，  排序等参数，依然放在  url的后面， 例如 

 http://localhost:8080/myboot/students?page=1&pageSize=20

  



## 第十章 总结

### 1. 注解

Spring + SpringMVC + SpringBoot 

```properties
-----------------------------创建对象的----------------------------
@Controller: 放在类的上面，创建控制器对象，注入到容器中
@RestController: 放在类的上面，创建控制器对象，注入到容器中。
    作用: 是一个复合注解，拥有@Controller，@ResponseBody的功能。使用这个注解的类，里面的控制器方法的返回值都是数据，而不能再表示视图了。

@Service: 放在业务层的实现类上面，创建service对象，注入到容器
@Repository: 放在dao层的实现类上面，表示创建dao对象，放入到容器。但是我们并没有使用这个注解，是因为现在使用MyBatis框架，dao对象是MyBatis通过代理生成的。不需要使用@Repository、 所以没有使用。
@Component: 放在类的上面，创建此类的对象，放入到容器中。 

赋值的：
@Value: 简单类型的赋值，还可以使用通过获取配置文件者的数据（properties或yml）。 来进行赋值。     例如：@Value("${server.port}") private Integer port

@Autowired: 引用类型赋值自动注入的，支持byName, byType. 默认是byType。 放在属性的上面，也可以放在构造方法的上面。 推荐是放在构造方法的上面。
@Qualifer:  给引用类型赋值，指定使用byName方式。@Autowird, @Qualifer都是Spring框架提供的。

@Resource: 来自jdk中的定义， javax.annotation。 实现引用类型的自动注入， 支持byName, byType. 默认是byName, 如果byName失败， 再使用byType注入。在属性上面使用。


----------------------------其它-----------------------------
@Configuration: 放在类的上面，表示这是一个（JavaConfig）配置类，相当于xml配置文件

@Bean: 放在方法的上面， 把方法的返回值对象，注入到spring容器中。

@ImportResource: 加载其他的xml配置文件，把XML配置文件中的对象注入到spring容器中

@PropertySource: 读取其他的properties属性配置文件

@ComponentScan: 扫描器 ，指定包名，扫描注解的

@ResponseBody: 放在方法的上面，表示方法的返回值是数据， 不是视图
@RequestBody : 把请求体中的数据，读取出来， 转为java对象使用。

@ControllerAdvice:  控制器增强。放在controller类的上面， 表示此类提供了一些增强方法。
@ExceptionHandler : 放在方法的上面，表示被注解的方法是用于处理全局异常的

@Transcational :  处理事务的， 放在service实现类的public方法上面， 表示此方法有事务


------------------------在SpringBoot中独有的注解---------------------------------
@SpringBootApplication: 放在启动类上面， 包含了@SpringBootConfiguration、                          @EnableAutoConfiguration、@ComponentScan


------------------------SpringBoot中与MyBatis相关的注解---------------------------------
@Mapper: 放在类的上面 ， 让MyBatis找到接口， 创建他的代理对象    
@MapperScan: 放在主类的上面 ， 指定扫描的包， 把这个包中的所有接口都创建代理对象。 对象注入到容器中
@Param: 如果dao接口有多个形参，则将其放在dao接口的形参前面，作为命名参数以供在MyBatis中写SQL语句时区分各个参数。
    
------------------------SpringBoot中与Dubbo相关的注解--------------------------------- 
@DubboService: 在提供者端使用的，暴露服务的， 放在接口的实现类上面
@DubboReference:  在消费者端使用的， 引用远程服务， 放在属性上面使用。
@EnableDubbo : 放在主类上面， 表示当前引用启用Dubbo功能。
```


