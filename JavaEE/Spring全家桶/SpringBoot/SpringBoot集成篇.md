# SpringBoot集成篇





## 第六章 Redis

Redis ： 一个NoSQL数据库，  常用作 缓存使用 （cache）

Redis的数据类型： string  ,  hash  ,set  ,zset , list



Redis是一个中间件： 是一个独立的服务器。

java中著名的客户端： Jedis ，  lettuce ， Redisson



​		SpringBoot中使用RedisTemplate（StringRedisTemplate）实现和Redis的交互。<u>RedisTemplate底层实际上使用的是  `lettuce` 客户端库。</u>

```xml
<!--redis起步依赖： 直接在项目中使用RedisTemplate(StringRedisTemplate)-->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!-- 
	data-redis使用的是 lettuce 客户端库；
	在程序中使用RedisTemplate类的方法操作redis数据，实际就是调用的lettuce 客户端中的方法。
-->
```





### 1. StringRedisTemplate和RedisTemplate区别

- StringRedisTemplate：使用**String的序列化**，把k，v 序列化之后存到redis。使用Redis命令行可以直接查看未乱码的数据。

- RedisTemplate ： 使用**JDK的序列化**，把k，v 序列化之后存到redis。使用Redis命令行会查看到乱码的数据。<u>默认是JDK的序列化方式，但是可以修改为其它的序列化方式。</u>



- 序列化：把对象转化为可传输的字节序列过程称为序列化。

- 反序列化：把字节序列还原为对象的过程称为反序列化。



- 为什么需要序列化

  ​		序列化最终的目的是为了对象可以跨平台存储，和进行网络传输。而我们进行跨平台存储和网络传输的方式就是IO，而我们的IO支持的数据格式就是字节数组。我们必须在把对象转成字节数组的时候就制定一种规则（序列化），那么我们从IO流里面读出数据的时候再以这种规则把对象还原回来（反序列化）。



- 什么情况下需要序列化

  ​		通过上面我想你已经知道了凡是需要进行“跨平台存储”和”网络传输”的数据，都需要进行序列化。本质上存储和网络传输 都需要经过 把一个对象状态保存成一种跨平台识别的字节格式，然后其他的平台才可以通过字节信息解析还原对象信息。



- 常用的序列化方式

  ​		序列化只是一种拆装组装对象的规则，那么这种规则肯定也可能有多种多样，比如现在常见的序列化方式有：JDK（不支持跨语言）、JSON、XML、Hessian、Kryo（不支持跨语言）、Thrift、Protofbuff……

  - **java的序列化**：把java对象转为`byte数组`，二进制数据
  - **json序列化**：json序列化功能将对象转换为 JSON 格式或从 JSON 格式转换对象。例如把一个Student对象转换为JSON字符串{"name":"李四", "age":29} )，反序列化(将JSON字符串 {"name":"李四", "age":29} 转换为Student对象)



- 设置key或者value的序列化方式

```java
// 使用RedisTemplate ，在存取值之前，设置序列化
// 设置 key 使用String的序列化
redisTemplate.setKeySerializer( new StringRedisSerializer());

// 设置 value 的序列化
redisTemplate.setValueSerializer( new StringRedisSerializer());

redisTemplate.opsForValue().set(k,v);
```



- `HashOperations`和`BoundHashOperations` 区别

> ​		如果对相同的key进行操作的时候，使用`HashOperations`会显得冗余：因为每次都需要显示绑定key。
>
> ​		但是如果使用`BoundHashOperations`，只需要在声明的时候显示绑定一次key，以后再去对同样的key进行操作的时候，直接使用即可。

```java
/**获取a，然后获取b，然后删除c，对同一个key有多次操作，按照opsForHash()的写法
 * 每次都是redisTemplate.opsForHash().xxx("key","value")写法很是啰嗦
 */
int result = (Integer) redisTemplate.opsForHash().get("hash-key","a");
result = (Integer)redisTemplate.opsForHash().get("hash-key","b");
redisTemplate.opsForHash().delete("hash-key","c");

/**
 * boundHashOps()则是直接将key和boundHashOperations对象进行了绑定，
 * 后续直接通过boundHashOperations对象进行相关操作即可，写法简洁，不需要
 * 每次都显式的将key写出来
 */
BoundHashOperations<String, String, Object> boundHashOperations = redisTemplate.boundHashOps("hash-key");
result = (Integer) boundHashOperations.get("a");
result = (Integer) boundHashOperations.get("b");
boundHashOperations.delete("c");
```





## 第七章 SpringBoot集成Dubbo

### 1.  看 SpringBoot集成Dubbo的文档



 https://github.com/apache/dubbo-spring-boot-project/blob/master/README_CN.md



### 2. 公共项目

独立的maven项目： 定义了接口和数据类

```java
public class Student implements Serializable {
    private static final long serialVersionUID = 1901229007746699151L;

    private Integer id;
    private String name;
    private Integer age;
}

public interface StudentService {

    Student queryStudent(Integer id);
}

```



### 3.  提供者

创建SpringBoot项目

1） pom.xml

```xml
<dependencies>

   <!--加入公共项目的gav-->
   <dependency>
      <groupId>com.bjpowernode</groupId>
      <artifactId>022-interface-api</artifactId>
      <version>1.0.0</version>
   </dependency>

   <!--dubbo依赖-->
   <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-spring-boot-starter</artifactId>
      <version>2.7.8</version>
   </dependency>


   <!--zookeeper依赖-->
   <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-dependencies-zookeeper</artifactId>
      <version>2.7.8</version>
      <type>pom</type>
      <exclusions>
         <!-- 排除log4j依赖 -->
         <exclusion>
            <artifactId>slf4j-log4j12</artifactId>
            <groupId>org.slf4j</groupId>
         </exclusion>
      </exclusions>
   </dependency>
</dependencies>
```



2）实现接口

```java
/**
 * 使用dubbo中的注解暴露服务
 * @Component 可以不用加
 */
@DubboService(interfaceClass = StudentService.class,version = "1.0",timeout = 5000)
public class StudentServiceImpl implements StudentService {
    @Override
    public Student queryStudent(Integer id) {
        Student student  = new Student();
        if( 1001 == id){
            student.setId(1001);
            student.setName("------1001-张三");
            student.setAge(20);
        } else if(1002  == id){
            student.setId(1002);
            student.setName("#######1002-李四");
            student.setAge(22);
        }

        return student;
    }
}
```



3）application.properties

```properties
#配置服务名称 dubbo:application name="名称"
spring.application.name=studentservice-provider

#配置扫描的包， 扫描的@DubboService
dubbo.scan.base-packages=com.bjpowernode.service

#配置dubbo协议
#dubbo.protocol.name=dubbo
#dubbo.protocol.port=20881

#注册中心
dubbo.registry.address=zookeeper://localhost:2181
```



4)在启动类的上面

```java
@SpringBootApplication
@EnableDubbo
public class ProviderApplication {

   public static void main(String[] args) {
      SpringApplication.run(ProviderApplication.class, args);
   }
}
```



### 4. 消费者

创建SpringBoot项目

1） pom.xml

```xml
<dependencies>

   <!--加入公共项目的gav-->
   <dependency>
      <groupId>com.bjpowernode</groupId>
      <artifactId>022-interface-api</artifactId>
      <version>1.0.0</version>
   </dependency>

   <!--dubbo依赖-->
   <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-spring-boot-starter</artifactId>
      <version>2.7.8</version>
   </dependency>


   <!--zookeeper依赖-->
   <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-dependencies-zookeeper</artifactId>
      <version>2.7.8</version>
      <type>pom</type>
      <exclusions>
         <!-- 排除log4j依赖 -->
         <exclusion>
            <artifactId>slf4j-log4j12</artifactId>
            <groupId>org.slf4j</groupId>
         </exclusion>
      </exclusions>
   </dependency>
</dependencies>
```

2)  创建了Controller 或者 Service都可以

```java
@RestController
public class DubboController {

    /**
     * 引用远程服务， 把创建好的代理对象，注入给studentService
     */
    //@DubboReference(interfaceClass = StudentService.class,version = "1.0")

    /**
     * 没有使用interfaceClass，默认的就是 引用类型的 数据类型
      */
    @DubboReference(version = "1.0")
    private StudentService studentService;

    @GetMapping("/query")
    public String queryStudent(Integer id){
        Student student   = studentService.queryStudent(id);
        return "调用远程接口，获取对象："+student;
    }
}
```



3）application.properties

```properties
#指定服务名称
spring.application.name=consumer-application
#指定注册中心
dubbo.registry.address=zookeeper://localhost:2181
```





### 5. 练习

使用的技术： SpringBoot ,Dubbo, Redis, MyBatis



Student表：

![image-20210119150418295](D:\course\25-SpringBoot\笔记\images\image-20210119150418295.png)	

CREATE TABLE `student` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) COLLATE utf8_bin DEFAULT NULL,
  `phone` varchar(11) COLLATE utf8_bin DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8 COLLATE=utf8_bin;

1) 注册学生

​     phone必须唯一， 如果已经存在了手机号， 注册失败的。 

​                 int addStudent(Student student);

​                返回值：int

​                 1： 注册成功

​                 2 ： 手机号已经存在  

​                                    

​      name至少两个字符，

​      age 必须 大于 0 

2） 查询学生，根据id查询，此学生。 

​        先到redis查询学生， 如果redis没有此学生，从数据库查询， 把查询到的学生放入到redis。

​      后面再次查询这个学生应该从redis就能获取到。



​        Student  queryStudent(Integer id);



3) 使用Dubbo框架，    addStudent, queryStudent 是有服务提供者实现的。

​    消费者可以是一个Controller ， 调用提供者的两个方法。  实现注册和查询。



4）页面使用html和ajax，jquery。

​       在html页面中提供 form 注册学生， 提供文本框输入id，进行查询。

​      注册和查询都使用ajax技术。



​    html，jquery.js都放到resources/static目录中

 





## 第八章  打包

### 1. 打包war

1.创建了一个jsp应用

2.修改pom.xml

1）指定打包后的war包名称。在部署到另外的服务器上之后，**项目根路径**将会是`finalName`中指定的名称。

```xml
<build>
   <!--打包后的文件名称-->
   <finalName>myboot</finalName>
</build>
```



2）如果要使用jsp，请指定jsp编译目录。并配置资源路径。

```xml
<!--resources插件， 把jsp编译到指定的目录-->
<resources>
   <resource>
      <directory>src/main/webapp</directory>
      <targetPath>META-INF/resources</targetPath>
      <includes>
         <include>**/*.*</include>
      </includes>
   </resource>

   <!--使用了mybatis ，而且mapper文件放在src/main/java目录-->
   <resource>
      <directory>src/main/java</directory>
      <includes>
         <include>**/*.xml</include>
      </includes>
   </resource>

   <!--把src/main/resources下面的所有文件，都包含到classes目录-->
   <resource>
      <directory>src/main/resources</directory>
      <includes>
         <include>**/*.*</include>
      </includes>
   </resource>
</resources>
```



3）将打包方式设置为war

```xml
<!--设置打包类型：设置在gav坐标标签下即可-->
<packaging>war</packaging>
```



4）主启动类继承`SpringBootServletInitializer`

> ​		就是原有的 web.xml 文件的替代。使用了嵌入式的 Servlet，默认不支持 jsp。
>
> ​		配置这个的目的就是将其作为一个web.xml文件，随打包一起，称为web应用。
>
> > 如果在打包时报错：
> >
> > ```txt
> > webxml attribute is required (or pre-existing WEB-INF/web.xml if executing in update mode)
> > ```
> >
> > - #### 请在pom.xml文件中添加配置，忽略掉web.xml的打包：
> >
> > ```xml
> > <properties>
> >     <failOnMissingWebXml>false</failOnMissingWebXml>
> > </properties>
> > ```

```java
/**
 * SpringBootServletInitializer: 继承这个类， 才能使用独立tomcat服务器
 */
@SpringBootApplication
public class JspApplication  extends SpringBootServletInitializer  {

   public static void main(String[] args) {
      SpringApplication.run(JspApplication.class, args);
   }

   @Override
   protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(JspApplication.class);
   }
}
```



|  在SpringBoot项目中打war包项目结构（注意：没有创建web.xml）  |
| :----------------------------------------------------------: |
| <img src="imgs\在SpringBoot项目中打war包项目结构.png" alt="在SpringBoot项目中打war包项目结构" style="zoom:67%;" /> |



5）部署war

 把war放到tomcat等服务器的发布目录中。 以tomcat为例， 将`myWeb.war`放到`'tomcatpath'/webapps`目录。将Tomcat启动之后，war包会被自动解压。如果没有自动解压，请检查`CATALINA_HOME`配置的是否正确。





### 2. 打包为jar

1.创建了一个包含了jsp的项目

2.修改pom.xml

​     1) 指定打包后的文件名称（即打包后的项目上下文路径）

```xml
<build>
   <!--打包后的文件名称-->
   <finalName>myboot</finalName>
</build>
```



  2) 指定springboot-maven-plugin版本

```xml
<plugins>
   <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <!--打包jar， 有jsp文件时，必须指定maven-plugin插件的版本是 1.4.2.RELEASE-->
      <version>1.4.2.RELEASE</version>
   </plugin>
</plugins>
```



3）最后执行 maven clean、package

​       在target目录中，生成jar 文件， 例子是myboot.jar

​       执行独立的springboot项目  在cmd中 执行 `java  -jar  myboot.jar`



### 3. 总结

​		打成war包，需要单独使用服务器来运行它。这样需要占用非常多的资源，但是可以充分利用服务器的能力来发挥项目的功能。

​		打成jar包，小巧、简单、方便，直接使用内嵌的web服务器就可以运行项目了。可是项目功能的性能释放肯定没有单独使用服务器运行时高。







## 第九章 Thymeleaf 模板引擎

Thymeleaf： 是使用java开发的模板技术， 在服务器端运行。 把处理后的数据发送给浏览器。

​         模板是作视图层工作的。  显示数据的。  Thymeleaf是基于Html语言。 Thymleaf语法是应用在

​        html标签中 。 SpringBoot框架集成Thymealeaf,  使用Thymeleaf代替jsp。



Thymeleaf 的官方网站：http://www.thymeleaf.org
Thymeleaf 官方手册：https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html



### 1. 表达式

1. 标准变量表达式 

   语法：  ${key} 

   作用： 获取key对于的文本数据，  key 是request作用域中的key ， 使用request.setAttribute(), model.addAttribute()

   在页面中的 html标签中， 使用 th:text="${key}" 

```html
<div style="margin-left: 400px">
    <h3>标准变量表达式:  ${key}</h3>
    <p th:text="${site}">key不存在</p>
    <br/>
    <p>获取SysUser对象 属性值</p>
    <p th:text="${myuser.id}">id</p>
    <p th:text="${myuser.name}">姓名</p>
    <p th:text="${myuser.sex}">姓名：m男</p>
    <p th:text="${myuser.age}">年龄</p>
    <p th:text="${myuser.getName()}">获取姓名使用getXXX</p>
</div>
```



2. 选择变量表达式（ 星号变量表达式）

   语法：  *{key}

   作用： 获取这个key对应的数据，   *{key}需要和th:object 这个属性一起使用。

   目的是简单获取对象的属性值。

   ```html
   <p>使用 *{} 获取SysUser的属性值</p>
   <div th:object="${myuser}">
       <p th:text="*{id}"></p>
       <p th:text="*{name}"></p>
       <p th:text="*{sex}"></p>
       <p th:text="*{age}"></p>
   
   </div>
   <p>使用*{}完成的表示 对象的属性值</p>
   <p th:text="*{myuser.name}" ></p>
   ```

3. 链接表达式

   语法： @{url}

   作用： 表示链接， 可以

   ```html
    <script src="..."> , <link href="..."> <a href=".."> ,<form action="..."> <img src="...">
   ```

   

### 2. Thymeleaf属性

属性是放在html元素中的，就是html元素的属性，加入了th前缀。  属性的作用不变。    加入上th， 属性的值由模板引擎处理了。  在属性可以使用变量表达式

例如：

```xml
<form action="/loginServlet" method="post"></form>

<form th:action="/loginServlet" th:method="${methodAttr}"></form>
```





### 3. each

each循环， 可以循环List，Array

语法：

在一个html标签中，使用th:each

```xml
<div th:each="集合循环成员,循环的状态变量:${key}">
    <p th:text="${集合循环成员}" ></p>
</div>

集合循环成员,循环的状态变量:两个名称都是自定义的。 “循环的状态变量”这个名称可以不定义，默认是"集合循环成员Stat"


```





each循环Map

在一个html标签中，使用th:each

```html
<div th:each="集合循环成员,循环的状态变量:${key}">
    <p th:text="${集合循环成员.key}" ></p>
    <p th:text="${集合循环成员.value}" ></p>
</div>

集合循环成员,循环的状态变量:两个名称都是自定义的。 “循环的状态变量”这个名称可以不定义，默认是"集合循环成员Stat"

key:map集合中的key
value：map集合key对应的value值

```





### 4. th:if

"th:if"  : 判断语句， 当条件为true， 显示html标签体内， 反之不显示 没有else语句

```xml
语法：
<div th:if=" 10 > 0 "> 显示文本内容 </div>

```



还有一个 th:unless  和 th:if相反的行为

```xml
语法：
<div th:unless=" 10 < 0 "> 当条件为false显示标签体内容 </div>
```



例子：if

```xml
<div style="margin-left: 400px">
        <h3> if 使用</h3>
        <p th:if="${sex=='m'}">性别是男</p>
        <p th:if="${isLogin}">已经登录系统</p>
        <p th:if="${age > 20}">年龄大于20</p>
        <!--""空字符是true-->
        <p th:if="${name}">name是“”</p>
        <!--null是false-->
        <p th:if="${isOld}"> isOld是null</p>
 </div>

```



例子： unless

```html
 <div style="margin-left: 400px">
        <h3>unless: 判断条件为false，显示标签体内容</h3>
        <p th:unless="${sex=='f'}">性别是男的</p>
        <p th:unless="${isLogin}">登录系统</p>
        <p th:unless="${isOld}"> isOld是null </p>
 </div>
```





### 5. th:switch

th:switch 和 java中的swith一样的

```html
语法：
<div th:switch="要比对的值">
    <p th:case="值1">
        结果1
    </p>
    <p th:case="值2">
        结果2
    </p>
    <p th:case="*">
        默认结果
    </p>
    以上的case只有一个语句执行
    
</div>
```





### 6. th:inline

1. 内联text：  在html标签外，获取表达式的值

   语法： 

   ```xml
   <p>显示姓名是：[[${key}]]</p>
   
    <div style="margin-left: 400px">
           <h3>内联 text, 使用内联表达式显示变量的值</h3>
           <div th:inline="text">
               <p>我是[[${name}]]，年龄是[[${age}]]</p>
               我是<span th:text="${name}"></span>,年龄是<span th:text="${age}"></span>
           </div>
   
           <div>
               <p>使用内联text</p>
               <p>我是[[${name}]],性别是[[${sex}]]</p>
           </div>
   </div>
   ```

   

2. 内联javascript

```html
例子：
 <script type="text/javascript" th:inline="javascript">
         var myname = [[${name}]];
         var myage = [[${age}]];

         //alert("获取的模板中数据 "+ myname + ","+myage)

        function fun(){
            alert("单击事件，获取数据 "+ myname + ","+ [[${sex}]])
        }
    </script>
```



### 7. 字面量

例子：

```html
 <div style="margin-left: 400px">
       <h3>文本字面量: 使用单引号括起来的字符串</h3>
       <p th:text="'我是'+${name}+',我所在的城市'+${city}">数据显示</p>

       <h3>数字字面量</h3>
        <p th:if="${20>5}"> 20大于 5</p>

        <h3>boolean字面量</h3>
        <p th:if="${isLogin == true}">用户已经登录系统</p>

        <h3>null字面量</h3>
        <p th:if="${myuser != null}">有myuser数据</p>
    </div>
```



### 8. 字符串连接

连接字符串有两种语法

1） 语法使用 单引号括起来字符串  ， 使用 + 连接其他的 字符串或者表达式

```html
  <p th:text="'我是'+${name}+',我所在的城市'+${city}">数据显示</p>
```

2）语法：使用双竖线， |字符串和表达式|

```html
<p th:text="|我是${name},我所在城市${city|">
    显示数据
</p>
```



例子：

```html
    <div style="margin-left: 400px">
       <h3>字符串连接方式1：使用单引号括起来的字符串</h3>
       <p th:text="'我是'+${name}+',我所在的城市'+${city}">数据显示</p>
        <br/>
        <br/>
        <h3>字符串连接方式2：|字符串和表达式|</h3>
        <p th:text="|我是${name},所在城市${city},其他人${myuser.name}|"></p>
    </div>
```



### 9. 运算符

```xml
算术运 算： + , - - , * , / , %
关系比较 : > , < , >= , <= ( gt , lt , ge , le )
相等判断： == , != ( eq , ne )


<div style="margin-left: 400px">
        <h3>使用运算符</h3>
        <p th:text="${age > 10}">年龄大于 10 </p>
        <p th:text="${ 20 + 30 }">显示运算结果</p>
        <p th:if="${myuser == null}">myuser是null</p>
        <p th:if="${myuser eq null}">myuser是null</p>
        <p th:if="${myuser ne null}">myuser不是null</p>

        <p th:text="${isLogin == true ? '用户已经登录' : '用户需要登录'}"></p>
        <p th:text="${isLogin == true ? ( age > 10 ? '用户是大于10的' : '用户年龄比较小') : '用户需要登录'}"></p>

    </div>

三元运算符：
 表达式  ？ true的结果 : false的结果

三元运算符可以嵌套

```



### 10. 内置对象

文档地址：https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#web-context-namespaces-for-requestsession-attributes-etc.



#request 表示 HttpServletRequest

#session 表示 HttpSession对象

session 表示Map对象的， 是#session的简单表示方式， 用来获取session中指定的key的值

​               #session.getAttribute("loginname") == session.loginname

这些是内置对象，可以在模板文件中直接使用。

```html
例子：
 <div style="margin-left: 350px">
        <h3>内置对象#request,#session，session的使用</h3>
        <p>获取作用域中的数据</p>
        <p th:text="${#request.getAttribute('requestData')}"></p>
        <p th:text="${#session.getAttribute('sessionData')}"></p>
        <p th:text="${session.loginname}"></p>

        <br/>
        <br/>
        <h3>使用内置对象的方法</h3>
        getRequestURL=<span th:text="${#request.getRequestURL()}"></span><br/>
        getRequestURI=<span th:text="${#request.getRequestURI()}"></span><br/>
        getQueryString=<span th:text="${#request.getQueryString()}"></span><br/>
        getContextPath=<span th:text="${#request.getContextPath()}"></span><br/>
        getServerName=<span th:text="${#request.getServerName()}"></span><br/>
        getServerPort=<span th:text="${#request.getServerPort()}"></span><br/>
</div>
```





###  11. 内置工具类

内置工具类型： Thymeleaf自己的一些类，提供对string， date ，集合的一些处理方法

#dates: 处理日器的工具类

#numbers:处理数字的

#lists: 处理list集合的

```xml
<div style="margin-left: 350px">
      <h3>日期类对象 #dates</h3>
      <p th:text="${#dates.format(mydate )}"></p>
      <p th:text="${#dates.format(mydate,'yyyy-MM-dd')}"></p>
      <p th:text="${#dates.format(mydate,'yyyy-MM-dd HH:mm:ss')}"></p>
      <p th:text="${#dates.year(mydate)}"></p>
      <p th:text="${#dates.month(mydate)}"></p>
      <p th:text="${#dates.monthName(mydate)}"></p>
      <p th:text="${#dates.createNow()}"></p>
      <br/>

      <h3>内置工具类#numbers，操作数字的</h3>
      <p th:text="${#numbers.formatCurrency(mynum)}"></p>
      <p th:text="${#numbers.formatDecimal(mynum,5,2)}"></p>

      <br/>
      <h3>内置工具类#strings,操作字符串</h3>
      <p th:text="${#strings.toUpperCase(mystr)}"></p>
      <p th:text="${#strings.indexOf(mystr,'power')}"></p>
      <p th:text="${#strings.substring(mystr,2,5)}"></p>
      <p th:text="${#strings.substring(mystr,2)}"></p>
      <p th:text="${#strings.concat(mystr,'---java开发的黄埔军校---')}"></p>
      <p th:text="${#strings.length(mystr)}"></p>
      <p th:text="${#strings.length('hello')}"></p>
      <p th:unless="${#strings.isEmpty(mystr)}"> mystring 不是 空字符串  </p>

      <br/>
      <h3>内置工具类#lists,操作list集合</h3>
      <p th:text="${#lists.size(mylist)}"></p>
      <p th:if="${#lists.contains(mylist,'a')}">有成员a</p>
      <p th:if="!${#lists.isEmpty(mylist)}"> list 集合有多个成员</p>

      <br/>
      <h3>处理null</h3>
      <p th:text="${zoo?.dog?.name}"></p>

  </div>
```



### 12. 自定义模板

模板是内容复用， 定义一次，在其他的模板文件中多次使用。

模板使用：

1.定义模板

2.使用模板



模板定义语法：

```html
th:fragment="模板自定义名称"

例如：
<div th:fragment="head">
    <p>
        动力节点-java开发
    </p>
    <p>
        www.bjpowernode.com
    </p>
</div>
```



引用模板语法：

```html
1) ~{templatename :: selector}
   templatename:  文件名称
   selector： 自定义模板名称
2）templatename :: selector
   templatename:  文件名称
   selector： 自定义模板名称

对于使用模板：有包含模板（th:include）， 插入模板(th:insert)
```

