# SpringMVC

## 一、SpringMVC概述

### 1.1 什么是SpringMVC

​		**<font color="red">SpringMVC是Spring框架的一部分（子框架）， 是对Servlet的封装。</font>**Spring MVC是一个基于MVC架构的用来简化web应用程序开发的**表现层**框架。它是Spring的一个**模块**，<u>无需像MyBatis一样需要中间整合层来整合</u> 。在web模型中，MVC是一种很流行的框架，通过把Model，View，Controller分离，把较为复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合。



### 1.2 SpringMVC优缺点

- 优点
  - 基于 MVC 架构，各层功能分工明确。可以实现解耦合。
  - 作为Spring框架一部分，Spring拥有的优点，它都拥有。更不用说能够使用 Spring 的 IoC 和 AOP了。
  - 可以十分灵活地实现**数据验证、格式化和数据绑定机制**，可以使用任意对象进行数据绑定操作。

- 缺点
  - 由于对项目分层过于细致，导致开发效率降低，并增加了程序员的开发成本。



### 1.3 SpringMVC容器与Spring容器的关系

[spring与springmvc父子容器](https://blog.csdn.net/liuxiao723846/article/details/125007370)

---


1. Spring和SpringMVC是**父子容器**关系。

2. Spring框架的核心思想是IOC容器，用来管理bean的生命周期，而一个项目中会包含很多容器，并且<u>它们分上下层关系</u>。目前最常用的一个场景是在一个项目中导入Spring和SpringMVC框架。而Spring和SpringMVC其实就是两个父子容器：Spring是父容器，SpringMVC是子容器。**在Spring父容器中注册的Bean对SpringMVC子容器是可见的；而在SpringMVC子容器注册的Bean对Spring父容器是不可见的。**

3. 按照官方文档推荐，根据不同的业务模块来划分出不同的容器，在不同的容器中注册不同的Bean。  SpringMVC主要就是为我们构建web应用程序，那么SpringMVC子容器用来注册web组件的Bean（Controller），如控制器、处理器映射、视图解析器等。而Spring容器用来注册其它的Bean，这些Bean通常是驱动应用后端的中间层和数据层组件（service、dao……）。

> ​		在实际项目中，一般情况下，只需要一个SpringMVC容器即可满足业务需求。但如果在项目中因为特殊需求需要配置两个SpringMVC容器（配置多个DispatcherServlet），则此时可以根据这个实际需求，选择是否配置创建Spring容器。



---

**图示**：

|             Spring与SpringMVC父子容器             |
| :-----------------------------------------------: |
| ![img](imgs\31bc37f8ba0f3553a5d7c1de6cc7899a.png) |

- `Servlet WebApplicationContext`：这是对J2EE三层架构中的web层进行配置，如控制器(controller)、视图解析器(view resolvers)等相关的bean。通过spring mvc中提供的`DispatchServlet`来加载配置，通常情况下，配置文件的名称为spring-servlet.xml。
- `Root WebApplicationContext`：这是对J2EE三层架构中的service层、dao层进行配置，如业务bean，数据源(DataSource)等。通常情况下，配置文件的名称为applicationContext.xml。在web应用中，其一般通过`ContextLoaderListener`来加载。



## 二、SpringMVC入门例子

1. 配置中央调度器

- `web.xml`

```xml
<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

    <!--需要指定SpringMVC主配置文件路径-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>

    <!--中央调度器servlet需要在Web容器启动之后被创建，而不是在用户发起请求的时候创建-->
    <load-on-startup>1</load-on-startup>
</servlet>
    <!--指定什么请求交给DispatcherServlet处理-->
<servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

> ​		在\<servlet/>中添加\<load-on-startup/>的作用是：标记是否在Web服务器（这里是Tomcat） 启动时会创建这个 Servlet 实例，即是否在 Web 服务器启动时调用执行该 Servlet 的 `init()`方 法，而不是在真正访问时才创建。 它的值必须是一个整数。
>
> - 当值`大于等于 0` 时，表示容器在启动时就加载并初始化这个 servlet，**数值越小，该 Servlet 的优先级就越高**，其被创建的也就越早； 
>
> - 当值`小于 0 或者没有指定`时，则表示该 Servlet 在真正被使用时才会去创建。
>
> - 当值`相同`时，容器会自己选择创建顺序。



2. SpringMVC主配置文件

- `springMVC.xml` / `dispatcherServlet.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 声明组件扫描器 -->
    <context:component-scan base-package="com.lujun61.controller"/>

    <!-- 配置视图解析器：帮助开发人员完善视图文件的路径 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!-- 前缀：视图文件的路径 -->
        <property name="prefix" value="/WEB-INF/view/"/>

        <!-- 后缀：视图文件的扩展名 -->
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```



## 三、SpringMVC处理静态资源

### 3.1 路径映射规则 

- *路径匹配的优先级*

1. 精确匹配（没有任何通配符出现）
2. 目录&通配符匹配（且pattern中在通配符前出现的*“精确”*路径越长，越优先）**例：/aa/bb/* > /aa/***
3. 文件扩展名匹配
4. `/`匹配

> ​		注意：`/**` 并不可以用来匹配路径。是存在于拦截器、过滤器中特殊的匹配规则。代表**任意请求、任意的字符、文件或者多级目录和目录中的文件**。
>
> ​		另外：`/*` 在拦截器、过滤器中表示拦截所有的文件夹，<u>不包含子文件夹</u>。

<hr>

​		"/*"可以匹配所有url，包括带扩展名的，一般只用在过滤器上。而"/"很多人理解成不能拦截带扩展名的，这种理解是错误的！它其实也能拦截“.js”，“.css”，".png"等静态资源的访问。看官方文档可知，它是tomcat的默认servlet，当其他的url-pattern匹配不上时都会走这个servlet。它除了能够处理静态资源还能够处理HTTP缓存请求，媒体（音频/视频）数据流和文件下载简历。所以如果我们的项目中配置了"/"，会覆盖掉tomcat中的default servlet。



- Web服务器**默认的Servlet**作用是什么？？？？？？

> - **处理静态资源**
> - **处理未映射到其它servlet的请求（在web.xml文件/处理器方法中没有对应的servlet去处理的请求）**
>
> ```xml
> <!-- The default servlet for all web applications, that serves static     -->
> <!-- resources.  It processes all requests that are not mapped to other   -->
> <!-- servlets with servlet mappings (defined either here or in your own   -->
> <!-- web.xml file).  This servlet supports the following initialization   -->
> <!-- parameters (default values are in square brackets):                  -->
> ```





### 3.2 方案一：使用\<default-servlet-handler>

<hr>

> ​		加入`<default-servlet-handler>`这个标签后，框架就会创建控制器对象`DefaultServletHttpRequestHandler`（类似我们自定义的MyController）。		
>
> ​		`DefaultServletHttpRequestHandler`这个对象把**所有的接收到的请求**都转发给Tomcat默认的servlet来处理。也就是说，如果Controller层的@RequestMapping注解配置了一个映射路径，并且某一个请求访问的路径是@RequestMapping注解配置的映射路径。但是， 此时由于加了`default-servlet-handler`这个标签，`DefaultServletHttpRequestHandler`这个对象把所有的接收到的请求都转发给Tomcat默认的servlet来处理，**<font color="blue">那么就不会走@RequestMapping注解下面的方法。</font>**自然也就无法处理用户发起的对动态资源的请求。
>
> > ​		要想解决这个问题，就需要**<font color="red">提升控制类中请求映射的优先级</font>**！！！
> >
> > - 具体做法就是加上`<mvc:annotation-driven/>`标签。
>
> 
>
> #### 理解使用`<mvc:annotation-driven/>`解决动、静态资源映射冲突问题
>
> > ​		因为Tomact容器中并不会存放<u>控制器对象</u>来处理用户对动态资源文件的访问，这些<u>控制器对象实际上是存在于SpringMVC容器之中</u>的。那么，要想让对动态资源的请求不被框架使用`DefaultServletHttpRequestHandler`转发到Tomcat，不就是**将对动态资源的请求映射的时间提升到资源被转发的时间之前**么？
> >
> > ​		这也就是为什么仅仅使用`DefaultServletHttpRequestHandler`处理用户请求将导致**动态资源文件**无法被处理。而添加上`<mvc:annotation-driven/>`就可以解决问题的原因。

- SpringMVC主配置文件

```xml
<!-- 
	解决 <mvc:default-servlet-handler/> 与 @RequestMapping 的冲突问题。
    即：解决动态资源和静态资源冲突的问题。
-->
<mvc:annotation-driven/>

<!--
	加入这个标签后，框架会自动创建 控制器对象：【DefaultServletHttpRequestHandler】
    DefaultServletHttpRequestHandler：这个对象负责将 >所有的请求< 转发给tomcat中自带的【DefaultServlet】。包括对动态资源文件的请求！！！
-->
<!--
	一般Web应用服务器默认的Servlet名称是"default"，因此DefaultServletHttpRequestHandler可以找到它。如果你所有的Web应用服务器的默认Servlet名称不是"default"，则需要通过default-servlet-name属性显示指定……
-->
<mvc:default-servlet-handler/>
```



- web.xml

```xml
<servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <!--
        当你的项目中使用了“/”，它会替代tomcat中的DefaultServlet接收用户请求；导致所有的静态资源请求都给DispatcherServlet处理，但是DispatcherServlet并没有处理静态资源的功能。
		当前 第一种方案 的解决方式就是将所有的对静态资源的请求转发给Tomcat（Web服务器）进行处理。
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```





### 3.3 方案二：使用\<mvc:resources/>

> ​		在方案一中，我们将静态资源的请求**转发**给了Tomcat进行处理，而并不是直接使用SpringMVC进行处理。
>
> ​		在方案二中，我们可以通过配置直接使用SpringMVC处理对静态资源的请求。

​		

​		在 Spring3.0 版本后，Spring 定义了专门用于处理静态资源访问请求的处理器 `ResourceHttpRequestHandler`。并且添加了标签，专门用于解决静态资源无法访问问题。如下所示：

```xml
<!--
    <mvc:resources> 加入后，框架会创建ResourceHttpRequestHandler这个处理器对象。
    ResourceHttpRequestHandler：处理用户对静态资源访问的请求，而不依赖于tomcat服务器。
    	mapping：访问静态资源的uri地址，使用通配符 “**”：表示当前目录下的>任意<路径
    	location：静态资源在项目中的目录位置。不可以使用通配符！！！
-->
<mvc:resources mapping="/static/**" location="/static/"/>


<!-- 解决动态资源和静态资源冲突的问题：提升@RequestMapping优先级 -->
<mvc:annotation-driven/>
```



### ==3.4 annotation-driven注解作用   To Be Continue~==

[(31条消息) 【spring mvc】annotation-driven 配置详解_云川之下的博客-CSDN博客](https://blog.csdn.net/m0_45406092/article/details/115436325)

[(31条消息) springmvc 中 注解驱动：＜mvc:annotation-driven＞的作用和使用_健康平安的活着的博客-CSDN博客_作用](https://blog.csdn.net/u011066470/article/details/112438252)

[(31条消息) springMVC配置注解驱动的作用_骑猪下不来的猴的博客-CSDN博客_springmvc注解驱动的作用](https://blog.csdn.net/weixin_42529699/article/details/88085405)



## 四、SpringMVC 核心技术

### 4.1 请求转发和重定向

​		**SpringMVC 框架把原来 Servlet 中的请求转发和重定向操作进行了封装。**现在可以使用简 单的方式实现转发和重定向。 **<font color="blue">只需要在视图完整路径之前加上forward或者redirect即可。</font>**作用是：**可以访问配置视图之后，无法访问的资源文件。**

- **forward：**表示转发，实现 `request.getRequestDispatcher("xx.jsp").forward();`
- **redirect：**表示重定向，实现 `response.sendRedirect("xxx.jsp");`

> ​		在SpringMVC项目中，无论是使用forward还是redirect，【项目根路径】都不需要添加。又是一个语法糖，加了就会错。
>
> ​		但是在Servlet中，只有在【请求转发】时，项目根路径才不用添加。小猫咪知道只能在本服务器内进行资源跳转。语法糖++。



​		**<font color="green">使用关键字之后，指定的视图路径将不再和视图解析器一同使用，就当项目中未配置视图解析器。</font>**




### 4.2 全局异常处理

​		将异常处理方法专门定义在一个类中，作为**全局的异常处理类**。
​    	需要使用注解`@ControllerAdvice`，字面理解就是“控制器增强”，是给控制器对象增强功能的。在使用@ControllerAdvice 修饰的类中使用`@ExceptionHandler`注解的方法，表示**全局异常处理方法。**
**​	    当使用@RequestMapping 注解修饰的方法抛出异常时，会执行@ControllerAdvice 修饰的类中的对应的异常处理方法。**
​	    @ControllerAdvice 注解被 @Component 注解 注解了，所以可以使用`<context:component-scan>`
扫描 @ControllerAdvice 所在的类路径(包名)，创建对象。

​		使用注解 @ExceptionHandler 可以将一个方法指定为异常处理方法。这个方法可以出现在被@ControllerAdvice注解的类中，也可以直接出现在@Controller中。但一般不建议直接写在Controller中，而是单独创建一个 **全局异常处理类** 来进行<u>异常处理</u>。当被@ExceptionHandler注解的方法直接写在Controller方法中的时候，表示只处理当前Controller中的异常。

- 把异常记录下来，记录到数据库、日志文件。
- 发送通知，把异常信息通过邮件、短信发送给相关人员
- 给用户友好的提示

​    	该注解只有一个`可选属性 value`，类型为一个 Class<?>数组，用于指定该方法所要处理的异常类，即所要匹配的异常。
​	    对于被@ExceptionHandler注解的方法，其返回值可以是 ModelAndView、String，或 void，方法名随意，
方法参数可以是 Exception 及其子类对象，表示捕获到的异常处理对象、HttpServletRequest、HttpServletResponse 等。系统会自动为这些参数赋值。



- 处理【NameException】异常

> ​		在value中指定需要处理的异常对象。

```java
@ExceptionHandler(value = NameException.class)
public ModelAndView doNameException(Exception ex) {
   
    ModelAndView mv = new ModelAndView();
    mv.addObject("msg", "用户必须是陆俊！");
    mv.addObject("exception", ex);

    mv.setViewName("nameError");
    return mv;
}
```



- 处理【其它类型】的异常

> ​		被没有配置value属性的@ExceptionHandler注解的异常处理类只能有一个。相当于 `if-else if-else` 中的**else**！！！

```java
@ExceptionHandler
public ModelAndView doOtherException(Exception ex) {
    
    ModelAndView mv = new ModelAndView();
    mv.addObject("msg", "其它类型的异常！");
    mv.addObject("exception", ex);

    mv.setViewName("defaultError");
    return mv;
}
```



- **扫描**全局异常处理类所在的包：使用<component-scan>

……

---

补充：待延伸~

- @ControllerAdvice
    - 用于修饰类，表示该类是Controller的全局配置类。
    - 在此类中，可以对Controller进行如下三种全局配置：
      异常处理方案、绑定数据方案、绑定参数方案。
- @ExceptionHandler
  - 用于修饰方法，该方法会在Controller出现异常后被调用，用于处理捕获到的异常。
- @ModelAttribute
  - 用于修饰方法，该方法会在Controller方法执行前被调用，用于为Model对象绑定参数。
- @DataBinder
  - 用于修饰方法，该方法会在Controller方法执行前被调用，用于绑定参数的转换器。





### 4.3 拦截器

​		SpringMVC 中的 Interceptor 拦截器是非常重要和相当有用的，它的主要作用是拦截指定的用户请求，并进行相应的预处理与后处理。其**拦截的时间点在“处理器映射器根据用户提交的请求映射出了所要执行的处理器类，并且也找到了要执行该处理器类的处理器适配器， 在处理器适配器执行处理器之前”。**当然，在处理器映射器映射出所要执行的处理器类时， 已经将拦截器与处理器组合为了一个处理器执行链，并返回给了中央调度器。



> 自定义拦截器，需要实现 `HandlerInterceptor` 接口。而该接口中含有三个方法： 
>
> ➢ **preHandle(request,response, Object handler)**： 该方法在处理器方法执行之前执行。其返回值为 boolean，若为 true，则紧接着会执行处理器方 法，且会将 afterCompletion()方法放入到一个专门的方法栈中等待执行。 
>
> ➢ **postHandle(request,response, Object handler,modelAndView)**：该方法在处理器方法执行之后执行。处理器方法若最终未被执行，则该方法不会执行。 由于该方法是在处理器方法执行完后执行，且该方法参数中包含 ModelAndView，所以该方法可以修 改处理器方法的处理结果数据，且可以修改跳转方向。 
>
> ➢ **afterCompletion(request,response, Object handler, Exception ex)**： 当 preHandle()方法返回 true 时，会将该方法放到专门的方法栈中，等到对请求进行响应的所有工作完成之后才执行该方法。即该方法是在中央调度器渲染（数据填充）了响应页面之后执行的。此时对 ModelAndView 再进行操作也对响应没有半点影响。 即afterCompletion 是最后执行的方法，通常用来释放资源。
>
> 
>
> ​		**<font color="blue">再次强调：当某一个拦截器的 preHandle()方法返回 true 并被执行到时，会向一个专门的方法栈中放入该拦截器的 afterCompletion()方法。</font>**

- 自定义拦截器：实现`HandlerInterceptor`

```java
/**
 * @description 拦截器：看做是多个Controller中公用的功能，集中到拦截器中统一处理。使用的是aop的思想
 * @author Jun Lu
 * @date 2022-01-06 23:27:51
 */
public class MyInterceptor implements HandlerInterceptor{
    /**
     * preHandle叫做【预处理方法】
     *     重要！是整个项目的门户！
     *     参数：Object handler--->被拦截的控制器对象
     *     返回值：
     *          true：通过了拦截器的验证，可以继续执行处理器方法
     *          false：未通过了拦截器的验证，不可以继续执行处理器方法
     *     特点：
     *      1、方法在控制器方法之前执行。用户的请求首先到达此方法
     *      2、在这个方法中可以获取请求信息，验证请求是否合法
     *
     *     注意：在项目中如果有多个拦截器，只要有一个拦截器的preHandle方法返回false，此次请求都将被视为不合法。
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                             Object handler) throws Exception {
        System.out.println("preHandle方法1执行了！");
        return true;
    }

    /**
     * postHandle叫做【后处理方法】
     *     参数：
     *         Object handler-------------->被拦截的控制器对象
     *         ModelAndView modelAndView--->处理器方法的返回值
     *     特点：
     *      1、方法在控制器方法之后执行
     *      2、在这个方法中可以获取【处理器方法的返回值】，并可以对其进行修正
     *
     *     注意：只有合法请求，拦截器中的postHandle方法才能被执行。
     *          所谓合法请求，就是指所有拦截器中的preHandle方法都返回了true。
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response,
                           Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle方法1执行了！");
    }

    /**
     * afterCompletion是最后执行的方法。
     *   【当前拦截器中preHandle返回值为true且被执行，其必定执行；为false，且未被执行，其必定不执行】
     *     参数：
     *         Object handler--->被拦截的控制器对象
     *         Exception ex----->程序中发生的异常
     *     特点：
     *      1、方法在【请求】处理完成后执行的。
     *          框架中规定：当视图处理完成后，就认为请求处理完成
     *      2、一般用来做资源的关闭操作
     *     注意：只有对应的此拦截器中的preHandle方法返回true并且成功执行的时候，afterCompletion方法才会被执行；
     *           如果对应的此拦截器中的preHandle方法返回false，那么afterCompletion方法将不会被执行。
     *           >可以理解为只要preHandle没有放行请求，此拦截器就未被激活参与项目使用，那么其中所有的方法都将不会被执行。<
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
                                Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion方法1执行了！");
    }
}

```



- 注册拦截器

```xml
<!-- 声明拦截器：拦截器可以有0~N个 -->
<mvc:interceptors>
    <!-- 声明第一个拦截器 -->
    <mvc:interceptor>
        <!-- 
			指定拦截器的请求地址URI
            path：就是URI地址，可以使用通配符 **（表示任意字符，文件或者多级目录和目录中的文件）
        -->
        <mvc:mapping path="/**"/>
        <!-- 声明拦截器对象 -->
        <bean class="com.lujun61.interceptor.MyInterceptor"/>
    </mvc:interceptor>


    <!-- 拦截器可以有多个；在框架中是以ArrayList集合的形式保存的 -->
    <mvc:interceptor>
        <!-- 需要被拦截的路径 -->
        <mvc:mapping path="/**"/>
        <!-- 放行的路径 -->
        <mvc:exclude-mapping path=""/>
        <bean class="com.lujun61.interceptor.MyInterceptor2"/>
    </mvc:interceptor>
</mvc:interceptors>
```



|                  多个拦截器的执行逻辑                  |
| :----------------------------------------------------: |
| ![多个拦截器的执行逻辑](imgs\多个拦截器的执行逻辑.png) |

- **总结**

> ​		从图中可以看出，只要有一个 preHandle()方法返回 false，则执行链都将被断开， 其后续的处理器方法与 postHandle()方法将无法执行。但无论执行链执行情况怎样，只要**方法栈**中有(`afterCompletion`)方法，即执行链中只要有 preHandle()方法返回 true，就会执行方法栈中的 afterCompletion()方法。最终都会给出响应。



#### a. 拦截器与过滤器的区别

​		**1.过滤器是servlet中的对象，  拦截器是框架中的对象**

​		**2.过滤器实现Filter接口的对象， 拦截器是实现HandlerInterceptor接口的对象**

​		**3.过滤器是用来设置request，response的参数，属性的，侧重对数据过滤的。拦截器是用来验证请求的，还可以直接截断请求。**

​		**<font color="blue">4.过滤器是在拦截器之前先执行的。</font>**

​		**5.过滤器是在tomcat服务器中创建的对象；拦截器是springmvc容器中创建的对象。**

​		**6.过滤器只有一个执行时间点；拦截器有三个执行时间点。**

​		**7.过滤器可以处理jsp，js，html等等**

​		  **拦截器是侧重拦截对Controller发起请求的对象。 <font color="red">对于静态资源，如果你的请求不能被DispatcherServlet接收，那么这个请求就不会被拦截器拦截。</font>**



#### b. 拦截器放行对静态资源的拦截

> ​		当我们将DispatcherServlet的\<url-pattern>映射规则修改为/之后，标志着DispatcherServlet将拦截静态资源和动态资源。此时，如果我们配置了拦截器，只要是能被DispatcherServlet<u>接收</u>的请求都会被拦截器拦截处理。而有的时候，我们并不希望拦截器拦截对某些静态资源的请求，此时，就需要做一些操作了。
>
> 
>
> ​		**<font color="blue">注意：前提是DispatcherServlet的\<url-pattern>映射规则修改为了`/`！！！</font>**

- **方案一**

> ​		提升Tomcat中**defaultServlet的优先级**。使其在dispatcherServlet接收处理请求之前处理静态资源请求。

```xml
<!-- 为了让静态资源文件不被拦截器拦截，直接将静态资源文件交给Tomcat的default Servlet处理 -->
<!-- 最好放在dispatcherServlet之前 -->
<!--
    将tomcat中的defaultServlet优先级设置为高于SpringMVC中的DispatcherServlet。
    像这样配置之后，静态资源请求将不再被dispatcherServlet接收，而是直接由defaultServlet处理。
-->
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>/static/*</url-pattern>
</servlet-mapping>
```



- **方案二**

> ​		配置拦截器**放行**对静态资源的请求。

```xml
<mvc:interceptor>
    <!-- 配置拦截器拦截的路径 -->
    <mvc:mapping path="/**"/>
    <!-- 配置拦截器 放行 对静态资源的请求 -->
    <mvc:exclude-mapping path="/static/**"/>
    <!--<mvc:exclude-mapping path="/**/*.png"/>-->
    <!-- 声明拦截器对象 -->
    <bean class="com.lujun61.interceptor.MyInterceptor"/>
</mvc:interceptor>
```



- **方案三**

> ​		打破前提，设置DispatcherServlet的\<url-pattern>映射规则为**后缀名匹配**。如*.do。





## 五、SpringMVC面试

### 5.1 SpringMVC请求处理流程

|                    SpringMVC请求处理流程                     |
| :----------------------------------------------------------: |
| ![image-20230127211410086](imgs\image-202301272106380913.png) |

> ​		注意：第5步的 执行**处理器**，在源码中是用`handle`表示的，而不是Controller。

**（1）浏览器提交请求到中央调度器**

**（2）中央调度器直接将请求转给处理器映射器。**

**（3）处理器映射器会根据请求，找到处理该请求的处理器（在源码中被称为handle），并将其封装为处理器执行链后返回给中央调度器。**

**（4）中央调度器根据处理器执行链中的处理器，找到能够执行该处理器的处理器适配器。**

**（5）处理器适配器调用处理器执行处理器执行链。**

**（6）处理器将处理结果及要跳转的视图封装到一个对象 ModelAndView 中，并将其返回给处理器适配器。**

**（7）处理器适配器直接将结果返回给中央调度器。**

**（8）中央调度器调用视图解析器，将 ModelAndView 中的<u>逻辑视图</u>名称封装为<u>物理视图</u>的视图对象。**

**（9）视图解析器将封装了的视图对象返回给中央调度器**

**（10）中央调度器调用视图对象，进行数据渲染，即进行数据填充，形成响应对象。**

**（11）中央调度器响应浏览器处理结果。**

<hr>

- 名词解析

**1、处理器映射器：**SpringMVC框架中的一种对象，框架中将实现了`HandlerMapping`接口的类都叫做【处理器映射器】。

​		**处理器映射器作用：**根据请求，从<u>SpringMVC容器</u>中获取<u>处理器对象</u>(`getBean("请求URI");`)。并将找到的处理器对象封装在【处理器执行链类（`HandlerExecutionChain`）】中保存。

**2、HandlerExecutionChain：**类中保存着【处理器对象】和【拦截器对象】执行顺序链。

**3、处理器适配器：**SpringMVC框架中的对象，需要实现`HandlerAdapter`接口。

​	**处理器适配器作用：**执行<u>处理器执行链</u>中的<u>处理器方法</u>。即：Controller类中的方法。并得到<u>返回值ModelAndView</u>。

**4、视图解析器：**SpringMVC框架中的对象，需要实现`ViewResolver`接口。

​	**视图解析器作用：**根据配置的前缀、后缀，组成视图的<u>完整路径</u>。创建View对象。

**5、View是一个接口，用来表示视图。**在框架中，jsp、html文件并不是使用字符串存储的，而是<u>使用View和它的实现类来表示。</u>

- 例：

  ​		**InternalResourceView：**视图类，用来表示JSP文件的。视图解析器会创建InternalResourceView类对象，调用方法，将Modle数据放入到ruquest作用域。执行对象视图的forward方法。结束请求。

**6、物理视图**：在控制器方法中直接通过 `ModelAndView.setView(View v)`方法设置视图对象，然后返回`ModelAndView`给前端控制器，这时的视图就是**物理视图**，因为前端控制器不需要再使用视图解析器来解析它，直接将视图内容响应给客户端即可；
**7、逻辑视图**：通过控制器方法返回一个字符串，或者是通过 ModelAndView.setViewName(String viewName) 方法设置一个视图名，此时的视图即为**逻辑视图**，因为前端控制器需要通过视图解析器来解析逻辑视图名，得到一个视图对象，然后才能响应给客户端。



[(31条消息) springmvc视图解析器详解_Eaeyson的博客-CSDN博客_springmvc视图解析器](https://blog.csdn.net/Eaeyson/article/details/125205228)











## To be Continue~

[(31条消息) SpringBoot之DispatcherServlet详解及源码解析_程序新视界的博客-CSDN博客_dispatcherservlet springboot](https://blog.csdn.net/wo541075754/article/details/103327737/)





























