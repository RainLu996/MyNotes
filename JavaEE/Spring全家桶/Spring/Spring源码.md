# Spring源码

## 一、refresh方法

- 名词理解

> 1. PostProcess：某个类的增强方法（后置处理方法）。

```java
public void refresh() throws BeansException, IllegalStateException {
    synchronized (this.startupShutdownMonitor) {
        /* Prepare this context for refreshing.   环境准备  */
        // 设置当前容器的启动日期和活动标志以及执行属性源的任何初始化
        prepareRefresh();  // 1

        /*                      准备 BeanFactory   2 3 4 5 6                */
        // Tell the subclass to refresh the internal bean factory.
        // 刷新BeanFactory
        // 初始化BeanFactory（此时创建的是一个空的BeanFactory）
        /* 进一步完善BeanFactory：读取Bean的 定义 信息。将Bean的定义信息 注册 到BeanFactory中
        Load bean definitions into the given bean factory, typically through delegating to one or more bean definition readers.
        */
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory(); // 2

        // Prepare the bean factory for use in this context.
        // 给BeanFactory中的属性值赋值：set、add、register、ignore……
        prepareBeanFactory(beanFactory);    // 3
        /* 至此，BeanFactory已经基本创建完成（BeanFactory的 基本属性值 成功被初始化）   */

        try {
            // Allows post-processing of the bean factory in context subclasses.
            // _注册_BeanPostProcessor
            // 留给子类扩展用的：给BeanFactory做一些增强操作（后置处理）
            postProcessBeanFactory(beanFactory);   // 4

            // Invoke factory processors registered as beans in the context.
            // 实例化并调用 所有子类实现的BeanFactory后置处理方法
            // 【4中的 BeanFactoryPostProcess 只是被创建（注册）出来，还没有被 调用 ！！！】
            invokeBeanFactoryPostProcessors(beanFactory);   // 5

            // Register bean processors that intercept bean creation.
            // _注册_BeanPostProcessor。此时未被调用执行！
            // 这也是留给子类做扩展用的：在Bean被【初始化之前、被初始化之后】这两个时间点做一些操作
            registerBeanPostProcessors(beanFactory);    // 6

            /*                 准备ApplicationContext    7 8 9 10 12        */
            // Initialize message source for this context.
            initMessageSource();    // 7

            // Initialize event multicaster for this context.
            initApplicationEventMulticaster();   // 8

            // Initialize other special beans in specific context subclasses.
            onRefresh();   // 9

            // Check for listener beans and register them.
            registerListeners();   // 10

            /*               11 为初始化 BeanFactory 中非延迟单例 bean       */
            // Instantiate all remaining (non-lazy-init) singletons.
            finishBeanFactoryInitialization(beanFactory);  // 11

            // Last step: publish corresponding event.
            finishRefresh();   // 12
        }

        catch (BeansException ex) {
            if (logger.isWarnEnabled()) {
                logger.warn("Exception encountered during context initialization - " +
                            "cancelling refresh attempt: " + ex);
            }

            // Destroy already created singletons to avoid dangling resources.
            destroyBeans();

            // Reset 'active' flag.
            cancelRefresh(ex);

            // Propagate exception to caller.
            throw ex;
        }

        finally {
            // Reset common introspection caches in Spring's core, since we
            // might not ever need metadata for singleton beans anymore...
            resetCommonCaches();
        }
    }
}
```



## 二、核心接口

Spring为了具备良好的扩展性，提供了许多的“特殊时机”接口。

- BeanDefinition：Bean实例的定义信息。它具有属性值、构造函数参数值的一系列Bean定义信息。<u>它的主要目的是允许`BeanFactoryPostProcessor`修改属性值和其他bean元数据。</u>

- BeanFactory：是访问Spring容器的根接口。

- BeanFactoryPostProcessor：允许自定义修改调整BeanFactory中的BeanDefinition信息。

  ​		**BeanFactoryPostProcessor可以与BeanDefinition进行交互并修改BeanDefinition，但决不能与Bean实例交互。**这样做可能会导致Bean过早的被实例化，违反容器并导致意外的副作用。如果需要bean实例交互，请考虑改为实现BeanPostProcessor。

  - postProcessBeanFactory：调整BeanFactory中的BeanDefinition信息的时机。

> ​		当BeanFactoryPostProcessor的所有实现类<u>被解析完毕</u>并且<u>被调用执行之后</u>，就开始修改BeanDefinition了。当BeanDefinition被修改完毕（即被“postProcess增强”）之后，BeanFactory容器就可以通过反射，根据这些BeanDefinition信息来实例化Bean对象了。如下demo：
>
> ```java
> Constructor constructor = clazz.getDeclareConstructor(); 
> Object obj = constructor.newInstance();
> ```
>
> 注意：此时仅仅只是进行了对象的**实例化（仅仅只调用了<font color="blue">无参构造方法</font>创建了一个空对象）**。
>
> - 创建对象的细致步骤
>   - 实例化：在堆中开辟空间，属性都是默认值。
>   - 初始化
>     - 给属性赋值
>     - 调用初始化方法==（在XML标签中指的是init-method）==，完成额外的扩展功能。
>
> > ​		但是Spring容器为了提高扩展性，在这些步骤的基础之上又提供了一些特殊时机。
> >
> > |                Bean生命周期                 |
> > | :-----------------------------------------: |
> > | ![Bean生命周期](imgs\Bean生命周期-初版.png) |

- BeanPostProcessor：与Bean实例进行交互。
  - postProcessBeforeInitialization：在Bean实例被初始化之前进行操作的时机
  - postProcessAfterInitialization：在Bean实例被初始化之后进行操作的时机



### Aware接口

​		Spring依赖注入的最大亮点是**所有的Bean对Spring容器的存在是没有任何感知的**，所以，一般情况下，bean是无法访问容器，来获取容器中的相关资源的。因此，Spring容器中Bean的耦合度也是极低的。

​		Aware接口从字面上翻译过来是*感知、察觉……*的含义。单纯的bean（未实现Aware系列接口）是无法感知到容器的存在的；而实现了**<font color="blue">Aware系列接口</font>**的bean是可以感知到Spring容器、访问Spring容器的。**这些Aware系列接口增强了Spring Bean的功能，但是会导致Bean对Spring容器的耦合度大大提高。**

​		==注意：Aware本身只是一个**标记接口**，标记实现了Aware接口的bean有资格通过回调方法被Spring容器通知特定框架对象。==



- Aware系列接口的共性
  - 都以“Aware”结尾
  - 都继承了Aware接口
  - 接口内均定义了一个set方法

- Aware子接口中的set方法

  ​		每个子接口中都定义了set方法。而方法中的<u>形参</u>是接口命名`XXXAware`前面的内容“`XXX`”，也就是当前的Bean需要感知到Spring容器中的资源内容。所以我们需要在Bean中声明相关的成员变量来接收。



==先记录一下：**Aware系列接口子实现类**在`init-method方法`执行**<font color="red">之前</font>**。这是bean的生命周期的一部分。==

- demo：[Spring之Aware接口介绍](https://blog.csdn.net/qq_38526573/article/details/88095674)

























<hr>

​		**自动装配其实就是在BeanFactoryPostProcessor中对BeanDefinition的操作。**



<hr>



**注解也是一种扩展，需要注意注解扩展的时机。**


- 要使用@Autowired注解，就必须在 Spring 容器中声明 `AutowiredAnnotationBeanPostProcessor` 的Bean。

- 要使用@Resource、@PostConstruct、@PreDestroy等注解就必须声明`CommonAnnotationBeanPostProcessor` 的Bean。

- 要使用@PersistenceContext注解，就必须声明`PersistenceAnnotationBeanPostProcessor`的Bean。

- 要使用 @Required的注解，就必须声明`RequiredAnnotationBeanPostProcessor`的Bean。

  ​		我们只需要使用`<context:annotation-config>`，就可以做到显示的向Spring容器中声明这些注解，而不用手动声明

```xml
AutowiredAnnotationBeanPostProcessor
CommonAnnotationBeanPostProcessor
PersistenceAnnotationBeanPostProcessor
RequiredAnnotationBeanPostProcessor
```



[context:annotation-config 和 context:component-scan](https://blog.csdn.net/qq_39304851/article/details/109388295)





