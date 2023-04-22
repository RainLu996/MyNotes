# MyBatis

沉淀沉淀再回头看[超全的MyBatis动态代理详解](https://blog.csdn.net/m0_64374605/article/details/122050926)

- Q：JDK 动态代理能否对类进行代理？

  ​	因为 JDK 动态代理生成的**<font color="green">代理类（类的前缀是$Proxy的类），会继承 Proxy 类并实现目标接口</font>**，由于 Java 无法多继承，所以无法对类进行代理。

- Q：抽象类是否可以使用 JDK 动态代理？

  ​	不可以，抽象类本质上也是类，Proxy 生成代理类的过程中，会校验传入的 Class 是否为接口。另一方面，由于java中的类不支持多继承，而JDK动态代理生成的代理类需要继承Proxy类然后实现一个目标接口。又由于抽象类只能被继承，不能被实现。所以，抽象类不可以被JDK动态代理。

- Q：Mybatis Mapper 接口没有实现类，怎么实现的动态代理？

  ​	Mybatis 会通过 Class.forname 得到 Mapper 接口 Class 对象，并生成对应的**动态代理对象**，核心业务处理是在**代理对象**的<u>接口实现方法</u>中调用 `InvocationHandler中的invoke方法`进行处理。

## 一、MyBatis入门

### 1.1 使用JDBC的缺陷

- 冗余代码比较多，开发效率低
- 需要关注 Connection ,Statement, ResultSet 对象的创建和销毁
- 对 ResultSet 查询的结果，需要自己进行结果封装
- 源程序和SQL语句直接混在一起，使得代码可维护性降低。因为在实际应用中，SQL语句的变化会很大，当SQL语句发生了变动，就意味着需要更改源程序，并且需要重新编译执行
- 给SQL语句传参很麻烦，特别是当遇到where条件十分多变的情形下，使用JDBC就显得力不从心了



### 1.2 了解MyBatis

- 什么是MyBatis？

​		myBatis是一个`半⾃动化ORM框架`，提供对数据库的操作能力，是一个【增强版的JDBC】。使用myBatis可以让开发人员集中精力写SQL语句，而不必关心Connection、Statement、ResultSet对象的创建，销毁和SQL语句的执行。<u>MyBatis在三层架构中负责持久层的操作，属于持久层框架。</u>



- MyBatis能做什么？

1. 提供了自动创建和管理Connection ,Statement, ResultSet的能力

2. 提供了自动执行sql语句的能力

3. 提供了将查询结果封装为Java对象的功能



- 为什么说MyBatis是半自动化的ORM框架？

  ​		Hibernate 属于全自动 ORM 映射框架，使用 Hibernate 查询关联对象或者关联集合对象时，Hibernate已经将SQL语句封装好了，我们可以根据`对象关系模型`直接获取，所以它是`全自动`的。而 Mybatis 在查询关联对象或关联集合对象时，需要`手动编写 SQL语句` 来**映射**这种对象关系模型。所以，被称之为`半自动 ORM 映射框架`。



### 1.3 使用MyBatis的优缺点

- 优点

1. 与JDBC相比，减少了50%以上的代码量。

  2. MyBatis是最简单的持久化框架，小巧并且简单易学。

  3. MyBatis灵活，不会对应用程序或者数据库的现有设计造成任何影响。SQL语句写在XML里，从程序代码中彻底分离，降低耦合度，便于统一管理和优化，并且可重用。
  4. 提供许多XML标签，用来编写满足不同要求的SQL语句，并支持编写动态SQL语句。
  5. 提供映射标签，支持对象与数据库的ORM字段关系映射（可以在XML中配置映射关系，也可以使用注解）。



- 缺点

1. SQL语句的编写工作量较大，尤其是字段多、关联表多时，更是如此，对开发人员编写SQL语句的功底有一定要求。
　　2. SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

---




> ​		综上所述：MyBatis专注于SQL本身，是一个足够灵活的DAO层解决方案。对于性能的要求很高，或者需求变化较多的项目，如互联网项目，MyBatis将是不错的选择。
>



### 1.4 什么是ORM

1. 定义

> ​		ORM是一种思想。ORM，即`Object-Relational Mapping`（对象关系映射），它的作用是在**关系型数据库**和**业务实体对象**之间作一个映射。这样，我们在具体操作业务对象的时候，就不需要再去和复杂的SQL语句打交道，只要像平时操作对象一样操作它就可以了 。

- O（Object）：Java虚拟机中的Java对象
- R（Relational）：关系型数据库
- M（Mapping）：将Java虚拟机中的Java对象映射到数据库表中的⼀行记录，或是将数据库表中⼀行记录映射成Java虚拟机中的⼀个Java对象。



2. 优缺点分析

- 优点
  - 隐藏`数据访问`细节，“封装”通用的`对数据库的交互操作`，这是ORM的核心。他使得我们对数据库的操作变的简单易行。

- 缺点
  - 无可避免的，自动化意味着映射和关联管理，代价是牺牲性能（早期，这是所有不喜欢ORM人的共同点）。现在的各种ORM框架都在尝试使用各种方法来减轻这块（LazyLoad，Cache），效果还是很显著的。

|                           图示ORM                            |
| :----------------------------------------------------------: |
| <img src="imgs\图示ORM.png" alt="图示ORM" style="zoom:50%;" /> |





### 1.5 MyBatis中的三个核心对象

- **SqlSessionFactoryBuilder**：解析MyBatis核心配置文件，创建SqlSessionFactory对象
- **SqlSessionFactory**：用来创建SqlSession对象
- **SqlSession**：用来进行一系列对数据库的操作

> 为避免导致并发问题，请了解核心对象生命周期：
>
> > 注意：
> >
> > ​		依赖注入框架可以创建`线程安全`的、`基于事务`的 SqlSession 和映射器，并将它们直接注入到你的 bean 中，因此可以直接忽略它们的生命周期。
>
> #### SqlSessionFactoryBuilder
>
> ​		这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了。 因此 SqlSessionFactoryBuilder 实例的最佳作用域是**<font color="red">方法作用域（也就是局部方法变量）</font>**。 虽然可以重用 SqlSessionFactoryBuilder 来创建多个 SqlSessionFactory 实例，但最好还是不要一直保留着它。因为，<u>它是通过解析MyBatis核心配置文件来创建SqlSessionFactory 实例</u>，如果一直保存，将会无法释放为了解析XML文件所占有的资源，造成资源浪费。
>
> #### SqlSessionFactory
>
> ​		SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。 使用 SqlSessionFactory 的最佳实践是在**<font color="green">应用运行期间不要重复创建多次</font>**，多次重建 SqlSessionFactory 被视为一种代码“坏习惯”。因此 SqlSessionFactory 的最佳作用域是**<font color="red">应用作用域</font>**。 有很多方法可以做到，最简单的就是使用<u>单例模式或者静态单例模式。</u>
>
> #### SqlSession
>
> ​		每个**<font color="green">线程</font>**都应该有它自己的 SqlSession 实例。SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是**<font color="red">请求或方法作用域</font>**。 绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。 也绝不能将 SqlSession 实例的引用放在任何类型的托管作用域中，比如 Servlet 框架中的 HttpSession。 如果你现在正在使用一种 Web 框架，考虑将 SqlSession 放在一个和 HTTP 请求相似的作用域中。 换句话说，每次收到 HTTP 请求，就可以打开一个 SqlSession，返回一个响应后，就关闭它。 这个关闭操作很重要，为了确保每次都能执行关闭操作，你应该把这个关闭操作放到 finally 块中。 





### 1.6 #{} 和 ${} 区别

1. #使用 ？在sql语句中做占位， 使用PreparedStatement执行sql，效率高
2. #能够避免sql注入，更安全
  3. $不使用占位符，是字符串连接方式，使用Statement对象执行sql，效率低
  4. $有sql注入的风险，缺乏安全性
  5. $可以替换表名或者列名
6. 变量替换后，#{}对应的变量自动加上**单引号**并进行字符串拼接；变量替换后，${}对应的变量不会加上**单引号**

>  	模糊查询Demo：
>
> - `like '%' #{name} '%'`：无sql注入的风险
> - `like '% ${name} %'`：有sql注入的风险



## 二、高级映射与延迟加载

> ​		基本映射只是**<font color="green">单表映射</font>**。
>
> ​		高级映射主要还是映射，只是映射中的数据关系更复杂了，包括**<font color="green">一对一、一对多、多对多</font>**的关系。

- `association`【**用于一对一和多对一**】：一个复杂类型的关联；许多结果将包装成这种类型嵌套结果映射。关联可以是 resultMap 元素，或是对其它结果映射的引用；
- `collection`【**用于一对多的关系**】：一个复杂类型的集合；嵌套结果映射，集合可以是 resultMap 元素，或是对其它结果映射的引用。
- `discriminator`：使用结果值来决定使用哪个 resultMap
- `case`：基于某些值的结果映射。嵌套结果映射，case 也是一个结果映射，因此具有相同的结构和元素；或者引用其它的结果映射



### 2.1 表准备

|                        班级表与学生表                        |
| :----------------------------------------------------------: |
| <img src="imgs\班级表.png" alt="班级表" style="zoom:67%;" /> |
| <img src="imgs\学生表.png" alt="学生表" style="zoom:67%;" /> |

- 实体类

```java
public class Student {
     private Integer sid;
     private String sname;
     //......
}

public class Clazz {
     private Integer cid;
     private String cname;
     //......
}
```

- Mapper接口自行意会



### 2.2 多对一

多种方式，常见的包括三种：

- 第⼀种方式：⼀条SQL语句（多表关联查询），并在`<resultMap>`标签中使用**级联*属性*映射**。
- 第⼆种方式：⼀条SQL语句（多表关联查询），并在`<resultMap>`标签中使用`<association>`标签。
- 第三种方式：两条SQL语句（分步查询）。这种方式常用：优点一是**可复用**。优点二是**⽀持懒加**
  **载**。



1. **第⼀种方式：级联属性映射**

```java
// 在Student中添加⼀个属性：Clazz clazz; 表示学⽣关联的班级对象。
public class Student {
     private Integer sid;
     private String sname;
     private Clazz clazz;
}
```

```xml
// Mapper映射文件
<mapper namespace="">
    // 映射配置
     <resultMap id="studentResultMap" type="Student">
         <id property="sid" column="sid"/>
         <result property="sname" column="sname"/>
         // 属性级联（class为属性，‘属性.子属性’配置映射关系）
         <result property="clazz.cid" column="cid"/>
         <result property="clazz.cname" column="cname"/>
     </resultMap>
    // 查询一步到位：多表关联查询
     <select id="selectBySid" resultMap="studentResultMap">
         select s.*, c.* 
         from t_student s 
         join t_clazz c on s.cid = c.cid 
         where sid = #{sid}
     </select>
</mapper>
```



2. **第⼆种方式：使用association标签**

- 修改结果映射

```xml
<resultMap id="studentResultMap" type="Student">
     <id property="sid" column="sid"/>
     <result property="sname" column="sname"/>
    // 学⽣对象 关联 ⼀个班级对象
     <association property="clazz" javaType="Clazz">
         <id property="cid" column="cid"/>
         <result property="cname" column="cname"/>
     </association>
</resultMap>

// 查询仍然是一步到位：多表关联查询
<select id="selectBySid" resultMap="studentResultMap">
    select s.*, c.* 
    from t_student s 
    join t_clazz c on s.cid = c.cid 
    where sid = #{sid}
</select>
```



3. **第三种方式：分步查询**

- StudentMapper.xml 

```xml
<resultMap id="studentResultMap" type="Student">
     <id property="sid" column="sid"/>
     <result property="sname" column="sname"/>
     <association property="clazz"
     select="xxx.ClazzMapper.selectByCid" column="cid"/>
</resultMap>

<select id="selectBySid" resultMap="studentResultMap">
    select s.* from t_student s where sid = #{sid}
</select>
```



- ClazzMapper.xml

```xml
<mapper namespace="xxx.ClazzMapper">
     <select id="selectByCid" resultType="Clazz">
	     select * from t_clazz where cid = #{cid}
     </select>
</mapper>
```



> 分步优点：
>
> - 第⼀个优点：代码复用性增强。
> - 第⼆个优点：支持延迟加载（Lazy Load）。【暂时访问不到的数据可以先不查询。提高程序的执行效率。】



### 2.3 多对一延迟加载

> ​		要想支持延迟加载，只需要在`association标签`中添加`fetchType="lazy"`即可。



- 修改分步查询结果映射

```xml
<resultMap id="studentResultMap" type="Student">
     <id property="sid" column="sid"/>
     <result property="sname" column="sname"/>
     <association property="clazz"
     	select="xxx.ClazzMapper.selectByCid" column="cid"
     	fetchType="lazy"/>   <!--启用懒加载-->
    <!-- 后续如果需要使用到学⽣所在班级的信息，才会执行关联的sql语句。-->
</resultMap>

<select id="selectBySid" resultMap="studentResultMap">
    select s.* from t_student s where sid = #{sid}
</select>
```





### 2.4 MyBatis全局开启延迟加载

```xml
<settings>
	 <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```

> ​		开启**全局延迟加载**之后，**所有的sql都会支持延迟加载。**
>
> ​		如果某个sql不希望它⽀持延迟加载，就将`fetchType`设置为`eager`。这样，针对某个特定的sql，就关闭了延迟加载机制。



<hr>



### 2.5 一对多

> ​		⼀对多的实现，通常是在`⼀`的⼀⽅中有`List集合`属性。

⼀对多的实现通常包括两种实现方式：

- 第⼀种方式：使用`<collection>`标签，配置映射关系，使用一条SQL语句多表关联查询

- 第⼆种方式：分步查询，使用`<collection>`标签的`select`属性，分步查询



```java
// 在Clazz类中添加表示学生信息的List集合
public class Clazz {
     private Integer cid;
     private String cname;
     private List<Student> stus;
     // ………… 
}
```



1. **第⼀种方式：使用collection标签**

```xml
<resultMap id="clazzResultMap" type="Clazz">
     <id property="cid" column="cid"/>
     <result property="cname" column="cname"/>
    <!--在collection标签中配置属性映射关系-->
    <!--ofType，表示“集合中的类型”。-->
     <collection property="stus" ofType="Student">
         <id property="sid" column="sid"/>
         <result property="sname" column="sname"/>
     </collection>
</resultMap>
<!--多表关联一步到位-->
<select id="selectClazzAndStusByCid" resultMap="clazzResultMap">
    select * 
    from t_clazz c 
    join t_student s on c.cid = s.cid 
    where c.cid = #{cid}
</select>
```



2. **第二种方式：分步查询**

- ClazzMapper.xml 

```xml
<resultMap id="clazzResultMap" type="Clazz">
     <id property="cid" column="cid"/>
     <result property="cname" column="cname"/>
     <!--主要看这⾥-->
     <collection property="stus"
     select="xxx.StudentMapper.selectByCid" column="cid"/>
</resultMap>

<!--sql语句也变化了-->
<select id="selectClazzAndStusByCid" resultMap="clazzResultMap">
	 select * from t_clazz c where c.cid = #{cid}
</select>
```



- StudentMapper.xml

```xml
<select id="selectByCid" resultType="Student">
	select * from t_student where cid = #{cid}
</select>
```



### 2.6  ⼀对多延迟加载

⼀对多延迟加载机制和多对⼀是⼀样的。同样是通过两种方式：

- 第⼀种：fetchType="lazy" 
- 第⼆种：修改全局的配置setting，`lazyLoadingEnabled=true`，如果开启全局延迟加载，想让某个 sql不使用延迟加载：`fetchType="eager"`





## 三、MyBatis的缓存

> ==**面试简版：**==
>
> ​		一级缓存是`SqlSession`级别的缓存，在**当前会话**中执行的DQL查询会储存进一级缓存，若出现相同的DQL查询，直接从一级缓存中获取结果。
>
> ​		当 SqlSession 的会话关闭了并且已经开启了二级缓存，那么该SqlSession中的一级缓存数据就会储存进二级缓存中。此时，若再此出现相同的DQL查询，就会走二级缓存。
>
> > ​		当 Mybatis 调用 Dao 层查询数据库时，先查询二级缓存，二级缓存中无对应数据，再去查询一级缓存，一级缓存中也没有，最后去数据库查找。

---



[MyBatis一级缓存和二级缓存](https://blog.csdn.net/xing_jian1/article/details/123943859)

> ​		**<font color="red">缓存的作用：通过减少查询数据库IO的次数，来提高程序的执行效率。</font>**		
>
> ​		mybatis的缓存：将`DQL语句`的查询结果放到缓存（内存）当中，下⼀次如果再次执行这条`DQL语句`，直接从缓存中取，不再查数据库。**⼀方面是减少了IO。另⼀方面不再执行繁琐的查找算法。**效率大大提升。

mybatis缓存包括：

- **⼀级缓存：一级缓存的作用域是`sqlSession`级别的。**使用同一个sqlSession执行相同的sql查询（相同的sql和参数），第一次会去查询数据库并写到缓存中，第二次从一级缓存中取。一级缓存是基于 `PerpetualCache` 的 HashMap 本地缓存。**<font color="red">默认打开一级缓存。</font>**
- <u>⼆级缓存：是Mybatis中SqlSessionFactory对象的缓存。</u>由同一个SqlSessionFactory对象创建的SqlSession共享其缓存。**二级缓存是 mapper 映射级别的缓存**，多个 SqlSession 去操作同一个 Mapper 映射的 sql 语句，多个SqlSession 可以共用二级缓存，二级缓存是跨 SqlSession 的。
- 集成其它第三方的缓存：比如EhCache【Java语言开发的】、Memcache【C语言开发的】
  等。开启了二级缓存后，还需要将`POJO类`实现`Serializable接口`。这是为了进行**<font color="blue">序列化和反序列化<u>缓存对象</u></font>**操作。因为二级缓存数据存储介质多种多样，不一定只存在内存中，有可能存在硬盘中。

**<font color="red">缓存只针对于DQL语句，也就是说缓存机制只对应`select语句`。</font>**



### 3.1 一级缓存

> ​		**<font color="red">⼀级缓存默认是开启的</font>**。不需要做任何配置。原理：只要使用`同⼀个SqlSession对象`执行`同⼀条SQL语句`，就会走缓存。



<hr>

1. 什么情况下不走一级缓存？ 

- 使用`不同的SqlSession对象`进行数据库操作
- 查询条件发生了改变



2. ⼀级缓存失效情况包括两种

- 第⼀次查询和第⼆次查询之间，手动清空了⼀级缓存

```java
sqlSession.clearCache();
```

- 第⼀次查询和第⼆次查询之间，执行了`增删改`操作并提交了事务。可以理解为：数据已经发生了变动，缓存中的数据需要被更新了（也就是需要被失效）



### 3.2 二级缓存

使用二级缓存需要具备以下几个条件：

1. `<setting name="cacheEnabled" value="true">`全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。默认就是true，无需设置。
2. 在需要使用**二级缓存**的`SqlMapper.xml`⽂件中添加配置标签：`<cache/>`
3. 使用二级缓存的**<font color="red">实体类对象必须是可序列化</font>**的，也就是必须实现`java.io.Serializable`接口
4. **<font color="red">SqlSession对象关闭或提交之后，⼀级缓存中的数据才会被写入到⼆级缓存当中。此时⼆级缓存才可用。</font>**【即：**<font color="blue">一级缓存的优先级高</font>**】



---


⼆级缓存的失效：

​		**只要两次查询之间出现了增删改并提交了事务，二级缓存就会失效。【一级缓存也会失效】**



<hr>

二级缓存中添加的`<cache/>`标签配置：

|                 二级缓存的相关配置                 |
| :------------------------------------------------: |
| ![二级缓存的相关配置](imgs\二级缓存的相关配置.png) |



1. eviction：指定从缓存中移除某个对象的淘汰算法。默认采用LRU策略。
    a. LRU：Least Recently Used。最近最少使用。优先淘汰在间隔时间内使用频率最低的对象。(其
    实还有⼀种淘汰算法LFU，即优先淘汰掉最不常用的对象。)
    b. FIFO：First In First Out。⼀种先进先出的数据缓存器。先进入二级缓存的对象最先被淘汰。
    c. SOFT：软引用。淘汰软引用指向的对象。具体算法和JVM的垃圾回收算法有关。
    d. WEAK：弱引用。淘汰弱引用指向的对象。具体算法和JVM的垃圾回收算法有关。

2. flushInterval：
    a. **⼆级缓存的刷新时间间隔（默认是1小时）**。单位<u>毫秒</u>。如果没有设置。就代表不刷新缓存，只要内存足够大，一
    直会向⼆级缓存中缓存数据。除非执行了增删改。

3. readOnly：

   a. true：多条相同的sql语句执行之后返回的对象是**共享的同⼀个**。性能好。但是**多线程并发可能**
   **会存在安全问题。**
   b. false：多条相同的sql语句执行之后返回的对象是<u>副本</u>，调用了clone方法。**性能⼀般。但安全。**

4. size：
    a. 设置⼆级缓存中最多可存储的**java对象数量**。默认值1024。



## 四、PageHelper分页

### 4.1 分页原理

​		PageHelper是mybatis的通用分页插件，通过mybatis的**拦截器**实现分页功能，<u>拦截sql查询请求，添加分页（limit）语句，最终实现分页查询功能。</u>

​		在调用dao的service方法中设置分页参数：`PageHelper.startPage(page, size)`，**分页参数会设置在ThreadLocal中**。<u>PageHelper在mybatis执行sql前进行拦截，从ThreadLocal中取出分页参数，修改当前执行的sql语句，添加分页sql。</u>最后执行添加了分页sql的sql语句，实现分页查询。分页结果被封装在`pageInfo`中。



### 4.2 使用 pageHelper 插件

1. Maven工程

- 引入依赖

```xml
<dependency>
     <groupId>com.github.pagehelper</groupId>
     <artifactId>pagehelper</artifactId>
     <version>5.3.1</version>
</dependency>
```



- 在MyBatis核心配置文件中引入分页核心插件（**分页拦截器**）

```xml
<plugins>
 	<plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```



- 代码编写

1. CarMapper.xml

```xml
<select id="selectAll" resultType="Car">
	select * from t_car
</select>
```



> #### pageHelper分页关键点
>
> - 在查询语句之前开启分页功能
> - 在查询语句之后封装PageInfo对象



```java
@Test
public void testPageHelper() throws Exception{
     SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("mybatis-config.xml"));
     SqlSession sqlSession = sqlSessionFactory.openSession();
     CarMapper mapper = sqlSession.getMapper(CarMapper.class);
     // 开启分⻚
     int pageNum = 1;    // 分页起始页码
     int pageSize = 2;   // 每页数据总数
     // 注意：必须要写在DQL语句之前。否则会失效！
     PageHelper.startPage(pageNum, pageSize);
    
     // 执⾏查询语句。查询语句只需要提供查询所有数据的SQL即可。
     // 分页拦截器会内部封装limit分页信息进行查询以及封装分页查询后的数据
     List<Car> cars = mapper.selectAll();
    
     // 获取分⻚信息对象：封装分页之后的信息
     int navigatePages = 5;  // 导航条页码个数
     PageInfo<Car> pageInfo = new PageInfo<>(cars, navigatePages);
     System.out.println(pageInfo);
}
```



2. SpringBoot工程

- 依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper‐spring‐boot‐starter</artifactId>
    <version>1.2.4</version>
</dependency>
```



- 添加配置

```yml
# 配置数据库为mysql ，因为不同数据库，有不同的方言
pagehelper:
    helper‐dialect: mysql
```















> 逆向工程
>
> - 什么是逆向：数据库表—>程序
> - 什么是正向：程序—>数据库表





























