# SpringBoot面试题

[大厂面试相关：精选41 道 Spring Boot 面试题，附答案！ - 脉脉 (maimai.cn)](https://maimai.cn/article/detail?fid=1455519864&efid=X0qhANzjme4Ft1ug5I58CA)

### Spring、SpringMVC和SpringBoot的区别

**1、Spring**

​		Spring是一个开源容器框架，简单的说就是一个**分层的轻量级开源框架**，可以接管web层（表现层），业务逻辑层，数据访问层的组件，配置和管理bean。其核心就是控制反转(IOC)和面向切面([AOP](https://so.csdn.net/so/search?q=AOP&spm=1001.2101.3001.7020))。

**2、SpringMVC**

​		Spring MVC属于[SpringFrameWork](https://so.csdn.net/so/search?q=SpringFrameWork&spm=1001.2101.3001.7020)的后续产品，已经融合在Spring Web Flow里面。SpringMVC是web层的MVC框架，用于替代servlet处理/响应请求，获取表单参数，表单校验等。（SpringMVC是一个MVC的开源框架，SpringMVC=struts2+spring，springMVC就相当于是Struts2加上Spring的整合）

**3、SpringBoot**

​		SpringBoot 是 Spring 开源组织下的子项目，是 Spring 组件的**一站式解决方案**。SpringBoot简化了使用 Spring 的难度，遵循“约定优于配置” ，提供了各种启动器，并支持自动配置，避免了繁重的配置，简化了应用的开发和部署，使得开发者能快速上手搭建一个企业级Spring项目。



### SpringBoot有哪些优缺点？

- 优点

    - **独立运行**：内嵌了servlet，Tomcat等，不需要打成war包部署到容器中，而只需要将SpringBoot项目打成jar包就能独立运行。
    - **简化配置**：启动器自动依赖一系列相关的组件，简少了 maven 的配置。各种常用组件及配置已经默认自动配置完成，无需过多干预。这也避免了手动管理项目依赖所造成的各种版本冲突问题。可以做到无冗余代码生成和无XML配置，这得益于JavaConfig的存在。
    - **应用监控**：SpringBoot 提供一系列端点可以监控服务及应用，做健康检测、注解配置监测。
    
- 缺点

    - 程序报错时的异常信息很笼统，不精确，导致排错时难以定位。

      

### SpringBoot 的配置文件有哪几种格式？它们有什么区别？

​		`		.properties` 和 `.yml`，它们的区别主要是**书写格式不同**。另外，.yml 格式不支持使用`@PropertySource` 注解导入配置。

- `.properties`

```properties
app.user.name = rainlu
```

- `.yml`

```yml
app:
  user:
    name: rainlu
```



###  SpringBoot 的核心配置文件有哪几个？它们的区别是什么？

​		SpringBoot 的核心配置文件是 `application` 和 `bootstrap` 配置文件。

- `application` 配置文件这个容易理解，主要用于 SpringBoot 项目的**自动化配置**；
- `bootstrap` 配置文件有以下几个应用场景：
    - 使用 Spring Cloud Config 配置中心时，这时需要在 bootstrap 配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；
    - 一些固定的不能被覆盖的属性；
    - 一些加密/解密的场景；

> ​		单纯做 SpringBoot 开发，可能不太容易遇到 bootstrap.properties 配置文件，但是在使用 SpringCloud 时，这个配置就会经常遇到了，特别是在需要加载一些远程配置文件的时侯。它比 applicaton 优先加载，配置信息在应用程序上下文的引导阶段生效。



### 读取配置文件的方式

- 读取==application主配置文件==：

在`application.yml`或者`application.properties`文件中添加：

```properties
info.address=USA
info.company=Spring
info.degree=high
```

|               ① 单独使用`@Value`注解的读取方式               |
| :----------------------------------------------------------: |
| <img src="https:////upload-images.jianshu.io/upload_images/25370657-e18628da0fcd7aa1.png?imageMogr2/auto-orient/strip" alt="img" style="zoom:150%;" /> |

|      ② 单独使用`@ConfigurationProperties`注解的读取方式      |
| :----------------------------------------------------------: |
| ![img](https:////upload-images.jianshu.io/upload_images/25370657-fe4ab78bc5fe3a06.png?imageMogr2/auto-orient/strip) |



- 读取==其它配置文件==：

资源目录下建立`noapplication.properties/yml`:

```properties
db.username=root
db.password=123456
```



|          ① 联合使用`@PropertySource` + `@Value`注解          |
| :----------------------------------------------------------: |
| ![img](https:////upload-images.jianshu.io/upload_images/25370657-3f34eb38693bc8f4.png?imageMogr2/auto-orient/strip) |

> ​		**注意：@PropertySource不支持yml文件读取**。



| ② 联合使用`@PropertySource` + `@ConfigurationProperties`注解 |
| :----------------------------------------------------------: |
| ![img](https:////upload-images.jianshu.io/upload_images/25370657-f2b01f9278c7d7e0.png?imageMogr2/auto-orient/strip) |



|            ③ 自动注入`Environment`属性的读取方式             |
| :----------------------------------------------------------: |
| ![img](https:////upload-images.jianshu.io/upload_images/25370657-73da0c7e10ecbf31.png?imageMogr2/auto-orient/strip) |

> ​		以上所有加载出来的配置都可以通过`Environment注入`获取到。



- **总结** 

    ​		从以上示例来看，SpringBoot可以通过`@PropertySource`，`@Value`，`@ConfigurationProperties`以及`注入Environment属性`来绑定变量。



### SpringBoot 的核心注解是哪个？它主要由哪几个注解组成的？

​		启动类上面的注解是`@SpringBootApplication`，它是 SpringBoot 的核心注解。它是一个复合注解，主要包含了以下 3 个注解：

- `@SpringBootConfiguration`：复合了 @Configuration 注解，实现配置文件的功能。代表当前是一个配置类；

- `@EnableAutoConfiguration`：打开自动配置的功能，也可以关闭某个自动配置的选项，如关闭数据源自动配置功能： @SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })；

- `@ComponentScan`：Spring组件扫描器。



### SpringBoot 需要独立的容器运行吗？

​		可以不需要，因为SpringBoot内置了tomcat/jetty等容器。但可以自定义配置其它容器。



### SpringBoot启动时都做了什么？

[SpringBoot启动都做了什么？看完就懂了！ - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/450295684?utm_id=0)

​		SpringBoot的启动，主要配置了环境 (environment)、事件监听 (listeners)、应用上下文 (applicationContext)，并基于以上条件，在容器中开始实例化我们需要的Bean，至此，通过SpringBoot启动的程序已经构造完成。



### 你如何理解 SpringBoot 中的 Starters？

​		Starters（启动器）包含了**某个应用场景**下的一系列集成到应用里面的**依赖jar包**和相关的一些**初始化配置**，使得可以一站式集成 Spring 及其他技术，而不需要到处找示例代码和依赖包。比如：想要创建一个简单的web项目，只要加入 spring-boot-starter-web 启动器依赖再进行一些简单属性配置就可以了。Starters包含了在项目中经常会使用到的依赖，它们能快速持续的运行，都是一系列得到支持的管理传递性依赖。



### SpringBoot 中的 starter 到底是什么?

​		首先，这个 Starter 并非什么新的技术点，基本上还是基于 Spring 已有功能来实现的。首先它提供了一个自动化配置类，一般命名为 XXXAutoConfiguration ，在这个配置类中通过条件注解来决定一个配置是否生效（条件注解就是 Spring 中原本就有的），然后它还会提供一系列的默认配置，也允许开发者根据实际情况自定义相关配置，然后通过类型安全的属性(spring.factories)注入将这些配置属性注入进来，新注入的属性会代替掉默认属性。正因为如此，很多第三方框架，我们只需要引入依赖就可以直接使用了。当然，开发者也可以自定义 Starter。



### SpringBoot 支持哪些日志框架？推荐和默认的日志框架是哪个？

​		SpringBoot 支持[JUL](https://blog.csdn.net/qq_35512802/article/details/125380559)(`java.util.Logging`)、Log4j、Lockback 作为日志框架，如果你使用 Starters 启动器，SpringBoot 将使用 Logback 作为默认日志框架。



### 如何在 SpringBoot 启动的时候运行一些特定的代码？

​		如果你想在SpringBoot启动的时候运行一些特定的代码，你可以实现接口 **ApplicationRunner** 或者 **CommandLineRunner** ，这两个接口实现方式一样，它们都只提供了一个run方法。



**启动顺序：**

​		如果启动的时候有多个ApplicationRunner和CommandLineRunner，想控制它们的启动顺序，可以实现 org.springframework.core.Ordered接口或者使用 org.springframework.core.annotation.Order注解



### SpringBoot自动配置的原理是什么？

[B站目前讲的最透彻的SpringBoot自动配置，大厂面试必备知识点#安员外很有码_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1NY411P7VX/?spm_id_from=333.337.search-card.all.click&vd_source=fc63803bb06c782d1a34636d7a7376bf)

1、SpringBoot启动的时候加载主配置类，扫描到@EnableAutoConfiguration注解，开启自动配置功能。其本质是利用`AutoConfigurationImportSelector`给容器中自动配置一些组件。

2、查看AutoConfigurationImportSelector，其中public String[] selectImports(AnnotationMetadata annotationMetadata)方法内 最终调用getCandidateConfigurations()方法

4、查看 getCandidateConfigurations(AnnotationMetadata metadata， AnnotationAttributes attributes)，获取候选的配置，这个是扫描所有jar包类路径下"META-INF/spring.factories"

5、然后把扫描到的这些文件包装成Properties对象。

6、从properties中获取到EnableAutoConfiguration.class类名对应的值，然后把他们添加在容器中。

　　**简而言之，整个过程就是将类路径下"META-INF/spring.factories"里面配置的所有EnableAutoConfiguration的值加入到容器中。**



> ​		自动装配，简单来说就是自动将导入的依赖所需要的bean注册到IOC容器里面，而不需要开发人员再去手动配置bean。在SpringBoot应用中，只需要在主启动类上面加上@SpringBootApplication注解就可以实现自动装配。这个注解是一个复合注解，真正的实现自动装配的注解是@EnableAutoConfiguration。自动依赖的实现主要有三个步骤：第一个是“引入Starter的时候，其中必须要包含**@Configuration配置类**，在这些个配置类中，需要通过@Bean来声明一系列需要被装配到IOC容器里的bean对象即需要被自动装配的对象”；第二个是将这些配置类的**全限定路径**注册进`classpath:META-INF/spring.factories`文件中；第三个是：SpringBoot遵循**约定优于配置**，在启动时就会加载这个文件，从而知道jar包中的配置类的位置，Spring在拿到对应的配置类的全限定路径后，再来加载这些配置类，并创建一系列的bean对象，以完成自动配置。



### SpringBoot 自动配置的原理

在 Spring 程序 main 方法中 添加@SpringBootApplication 或者@EnableAutoConfiguration。会自动去 maven 中读取每个 starter 中的 spring.factories 文件，该文件里配置了所有需要被创建 spring 容器中的 bean。

SpringBoot 在启动时扫描项目所依赖的 JAR 包，寻找包含 spring.factories 文件的 JAR

根据 spring.factories 配置加载 AutoConfigure 类

根据@Conditional 注解的条件，进行自动配置并将 Bean 注入 Spring Context



---



1. 从配置文件`META_INF/Spring.factories`中加载可能用到的自动配置类
2. **去重**：将exclude和excludeName属性携带的类排除
3. **过滤**：将满足条件（@Conditional）的自动配置类返回



### spring-boot-starter-parent 有什么用 ?

- 我们都知道，新创建一个 SpringBoot 项目，默认都是有 parent 的，这个 parent 就是 spring-boot-starter-parent ，spring-boot-starter-parent 主要有如下作用：

1. 定义了 Java 编译版本为 1.8 。
2. 使用 UTF-8 格式编码。
3. 继承自 spring-boot-dependencies，这个里边定义了依赖的版本，也正是因为继承了这个依赖，所以我们在写依赖时才不需要写版本号。
4. 执行打包操作的配置。
5. 自动化的资源过滤。
6. 自动化的插件配置。
7. 针对 application.properties 和 application.yml 的资源过滤，包括通过 profile 定义的不同环境的配置文件，例如 application-dev.properties 和 application-dev.yml。

- 总结就是打包用的
  



### SpringBoot如何实现打包

- 进入项目目录在控制台输入mvnclean package，clean是清空已存在的项目包，package进行打包
- 或者点击左边选项栏中的Maven，先点击`clean`，再点击`package`



### SpringBoot 打成的 jar 和普通的 jar 有什么区别 ?

- SpringBoot 项目最终打包成的 jar 是可执行 jar ，这种 jar 可以直接通过 java -jar xxx.jar 命令来运行，但**不可以作为普通的 jar 被其他项目依赖，即使依赖了也无法使用其中的类**。

- SpringBoot 的 jar 无法被其他项目依赖，主要还是他和普通 jar 的结构不同。普通的 jar 包，解压后直接就是包名，包里就是我们的代码，而 SpringBoot 打包成的可执行 jar 解压后，在 `BOOT-INF\classes` 目录下才是我们的代码，因此无法被直接引用。如果非要引用，可以在 pom.xml 文件中增加配置，将 SpringBoot 项目打包成两个 jar ，一个可执行，一个可引用。

    

### SpringBoot 中的监视器

​		SpringBoot actuator 是 Spring 启动框架中的重要功能之一。SpringBoot 监视器可帮助您访问生产环境中正在运行的应用程序的当前状态。例如创建了什么 bean、控制器中的映射、CPU 使用情况等等。然后可以将自动收集和审计健康状况（health）以及指标（metric）反馈到您的应用程序中。



### SpringBoot中如何解决跨域问题 ?

1、基于WebMvcConfigurerAdapter配置加入Cors的跨域

跨域可以在前端通过 JSONP 来解决，但是 JSONP 只可以发送 GET 请求，无法发送其他类型的请求，在 RESTful 风格的应用中，就显得非常鸡肋，因此我们推荐在后端通过（CORS，Cross-origin resource sharing）
来解决跨域问题。这种解决方案并非 SpringBoot 特有的，在传统的 SSM 框架中，就可以通过 CORS 来解决跨域问题，只不过之前我们是在 XML 文件中配置 CORS ，现在可以通过实现WebMvcConfigurer接口然后重写
addCorsMappings方法解决跨域问题。

2、创建一个filter解决跨域

项目中前后端分离部署，所以需要解决跨域的问题。 我们使用cookie存放用户登录的信息，在spring拦截器进行权限控制，当权限不符合时，直接返回给用户固定的json结果。 当用户登录以后，正常使用；

当用户退出登录状态时或者token过期时，由于拦截器和跨域的顺序有问题，出现了跨域的现象。 我们知道一个http请求，先走filter，到达servlet后才进行拦截器的处理，如果我们把cors放在filter里，就可以优先于权限拦截器执行。

3、controller配置CORS

controller方法的CORS配置，您可以向@RequestMapping注解处理程序方法添加一个@CrossOrigin注解，以便启用CORS（默认情况下，@CrossOrigin允许在@RequestMapping注解中指定的所有源和HTTP方法）

@CrossOrigin 表示所有的URL均可访问此资源

@CrossOrigin(origins = “http://127.0.0.1:8080”) 表示只允许这一个url可以跨域访问这个controller

@CrossOrigin这个注解用起来很方便，这个可以用在方法上，也可以用在类上。如果你不设置他的value属性，或者是origins属性，就默认是可以允许所有的URL/域访问。

```
　　-value属性可以设置多个URL。
　　-origins属性也可以设置多个URL。
　　-maxAge属性指定了准备响应前的缓存持续的最大时间。就是探测请求的有效期。
　　-allowCredentials属性表示用户是否可以发送、处理 cookie。默认为false
　　-allowedHeaders 属性表示允许的请求头部有哪些。
　　-methods 属性表示允许请求的方法，默认get，post，head。
```





### SpringBoot 中如何解决跨域问题 ?

​		跨域可以在前端通过 JSONP 来解决，但是 JSONP 只可以发送 GET 请求，无法发送其他类型的请求，在 RESTful 风格的应用中，就显得非常鸡肋，因此我们推荐在后端通过 （CORS，Cross-origin resource sharing） 来解决跨域问题。这种解决方案并非 SpringBoot 特有的，在传统的 SSM 框架中，就可以通过 CORS 来解决跨域问题，只不过之前我们是在 XML 文件中配置 CORS ，现在可以通过实现WebMvcConfigurer接口然后重写addCorsMappings方法解决跨域问题。

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
            .allowedOrigins("*")
            .allowCredentials(true)
            .allowedMethods("GET"， "POST"， "PUT"， "DELETE"， "OPTIONS")
            .maxAge(3600);
    }
}
```







### 什么是 CSRF 攻击？

CSRF 代表跨站请求伪造。这是一种攻击，迫使最终用户在当前通过身份验证的Web 应用程序上执行不需要的操作。CSRF 攻击专门针对状态改变请求，而不是数据窃取，因为攻击者无法查看对伪造请求的响应。

简单来说就是：攻击者盗取合法用户登录信息，通过盗取账户进行登录，然后以合法用户的身份发送恶意请求，但是请求对于服务器来说是合法的。



[CORS与CSRF](https://blog.csdn.net/fitzleopard/article/details/106794413)

- CORS(Cross Origin Resource Sharing)跨域资源分享：是一种机制，通过在HTTP响应头中加入特定字段限制不同域的资源请求。
- CSRF(Cross Site Request Forgery)跨站请求伪造：是一种web攻击手段，通过向服务器发送伪造请求，进行恶意行为的攻击手段。



---



[整理了17个经典的SpringBoot面试题 - 简书 (jianshu.com)](https://www.jianshu.com/p/0c3207508dc1)

[(73条消息) SpringBoot面试题（总结最全面的面试题！！！）_小杰要吃蛋的博客-CSDN博客_springboot面试题总结](https://blog.csdn.net/weixin_43122090/article/details/105463864?spm=1035.2023.3001.6557&utm_medium=distribute.pc_relevant_bbs_down_v2.none-task-blog-2~default~ESQUERY~Rate-2-105463864-bbs-604329235.pc_relevant_bbs_down_v2_default&depth_1-utm_source=distribute.pc_relevant_bbs_down_v2.none-task-blog-2~default~ESQUERY~Rate-2-105463864-bbs-604329235.pc_relevant_bbs_down_v2_default)

[2020最新整理版SpringBoot 面试题 - 简书 (jianshu.com)](https://www.jianshu.com/p/6d4ce1bce821)

![image-20230207212326031](imgs\image-20230207212326031.png)