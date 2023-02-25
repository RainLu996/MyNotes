# XML

> ​		可扩展的标记性语言。

## 一、作用

​		代替properties文件作为配置文件。因为properties文件的**无层级式**配置方式不便于管理和阅读，而XML文档使用的是配置与配置之间的**隶属关系**来进行属性配置。所以更加易于管理。



## 二、版本

​		XML诞生于1996年，并于2004年进行了一次更新，但并不受欢迎。所以作者停更，当前使用的版本依旧是1996的`1.0版本`。

```xml
<?xml version="1.0" encoding="UTF-8"?>
```





## 三、XML语法

1. HTML是从XML演化出来的一种编程语言。XML与HTML是父子关系
2. HTML绝大多数语法都来自于XML
3. 一个XML文档必须以一个【根目录标签】为开始，其它标签必须作为【根目录标签】的直接子标签或者间接子标签出现
4. HTML标签对于英文字母的大小是忽略不计的，但是XML标签必须区分英文字母大小写
5. HTML标签中的属性内容可以通过一对`""`包含，也可以不用。但是XML标签中的属性内容必须包含在一对`""`或者`''`中。



## 四、XML约束文档

[DTD与XSD的区别](https://blog.csdn.net/qq_17037733/article/details/80503560)

1. 约束文档作用

   ​	1) 设置可以在当前XML文档中声明的【标签类型名】

   ​    2) 设置可以在标签中出现的【属性名】

   ​	3) 设置标签之间父子关系和兄弟关系

   

2. XML约束文档分类

   1) **<font color="green">DTD(Document Type Definition)约束文档：</font>**简单约束文档

   2) **<font color="green">SCHEMA(XSD：XML Schemas Definition)约束文档：</font>**高级约束文档



3. DTD约束文档

- 使用说明

|    DTD约束文档（web-app_2_3.dtd）    |
| :----------------------------------: |
| ![DTD约束文档](imgs\DTD约束文档.png) |

```xml
<!-- 在页面中加上这几句 -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE web-app SYSTEM "web-app_2_3.dtd">

// ……
```



- 语法

```xml-dtd
1.<!ELEMENT 标签类型名>：声明可以在XmL文档中出现标签类型名
2.<!ATTLIST 标签类型名 属性名>：声明可以在当前标签内部使用的属性名称
3.<!ELEMENT 标签类型名 (子标签名?)>：子标签可以出现在父标签内部，也可以不出现。子标签如果出现，就只能出现一次
4.<!ELEMENT 标签类型名 (子标签名+)>：子标签必须出现在父标签内部，并可以出现多次
5.<!ELEMENT 标签类型名 (#PCDATA)>：当前标签没有子标签
6.<!ELEMENT 标签类型名 (子标签名*)>：子标签可以出现在父标签内部，也可以不出现。子标签如果出现可以出现多次
7.<!ELEMENT 标签类型名 (子标签名)>：子标签必须出现在父标签内部，且只能出现一次
8.<!ELEMENT 标签类型名 (……, (子标签名1|子标签名2))>：这两个子标签必须有一个子标签出现在父标签中，但是不能同时出现

--------------------------------------------------------------------------------
注意：子标签谁在左边，谁就得优先出现！
例如：(子标签名1, 子标签名2, ……)，子标签名1就必须先被配置（由上至下）
```



4. SCHEMA约束文档

- 使用说明

|  SCHEMA约束文档（web-app_2_5.xsd）   |
| :----------------------------------: |
| ![XSD约束文档](imgs\XSD约束文档.png) |



```xsd
<!-- 在页面中加上 -->
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
         
         // ……
         
</web-app>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

	// ……

</beans>
```



> - xmlns
>
>   ​	（XML Namespaces的缩写）是一个属性，是XML命名空间的声明。作用是**赋予命名空间一个唯一的名称。**可以根据这个名称，来使用来自于不同的约束文档中的标签。
>
>   <hr>
>
>   用法示例：在Spring项目中声明配置文件路径
>
> `<context:property-placeholder location="classpath:user.properties" />`
>
> 
>
> - 命名空间
>
>   ​		由于xml允许自定义标识，而不同的人可能会定义出拥有不同意义但是标签名相同的标签。这时，当文件交换或者共享的时候就容易产生错误。为了避免这种错误发生，XML采用命名空间声明，允许你通过一个网址指向来识别你的标识。在XML中的使用就体现在每个XML文档都具有若干个约束文档声明。



## 五、XML文件解析方式

**简单介绍：**

- DOM
  - 树型解析器，将读入的xml文档转换成树结构对象[Document] 
  - 基于内存的，不管文件有多大，都会将所有的内容预先装载到内存中
  - 可以读取xml文件内容，也可以向xml文件中插入数据，修改数据
- SAX
  - 流机制解析器，在读入xml文档时生成相应的事件
  - 基于事件的，当某个事件被触发时，才获取相应的xml的部分数据，占用内存较小
  - 按顺序一边读取数据，一边进行解析，在读取数据的时候会触发事件，每触发一次，就执行一次触发方法
  - 只能对xml文件内容进行读取，而不能在文件中插入或修改数据



**优缺点总结：**

- DOM：**要求解析器将整个XML文件全部加载到内存中**，生成一个 Document对象
  - 优点：元素和元素之间保留结构、关系，可以针对元素进行増删査改操作。**<font color="red">（可以理解为有了明确的结构关系就可以进行修改了）</font>**
  - 缺点：如果XML文件过大，可能会导致内存溢出
- SAX：速度更快，更加高效。**逐行扫描，边扫描边解析**，并且以事件驱动的方式来进行具体的解析，毎解析一行都会触发一个事件。
  - 优点：不会出现内存溢出的问题，可以处理大文件
  - 缺点：只能读，不能写。**<font color="red">（可以理解为没有明确的结构关系就不能写）</font>**

> 解析器就是根据不同的解析方式提供具体的实现，为了方便开发人员来解析XML，有一些方便操作的类库。
>
> - `dom4j`：比较简单的XML解析的类库
>
> - `Jsoup`：功能强大的DOM方式解析的类库，尤其对HTML的解析更加方便





## 六、使用Dom4j解析XML

### 6.1 DOM4J依赖

```xml
<!-- DOM4J原生依赖 -->
<dependencies>
    <dependency>
        <groupId>org.dom4j</groupId>
        <artifactId>dom4j</artifactId>
        <version>2.1.1</version>
    </dependency>
</dependencies>

<!-- 
	 DOM4J集成XPath：
	 Jaxen是一个开源的XPath库。
-->
<dependency>
    <groupId>jaxen</groupId>
    <artifactId>jaxen</artifactId>
    <version>1.2.0</version>
</dependency>
```



### 6.2 DOM4J原生语法

```java
//创建解析器对象
SAXReader saxReader = new SAXReader();

// 获取输入流
InputStream is = 
    ClassLoader.getSystemClassLoader().getResourceAsStream("mybatis-config.xml");

// 读XML文件，返回document对象。document对象是文档对象，代表了整个XML文件。
Document document = reader.read(is);


```



- Dom4j的常用API说明

> Element getRootElement();	获取XML文件的根节点
> String getName();	返回标签的名称
> List < Element > elements();	获取标签所有的子标签
> String arrtributeVallue(String name) ;	获取指定属性名称的属性值
> String getText();	获取标签的文本
> String elementText(String name);	获取指定名称的子标签的文本，返回子标签文本的值

```java
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.util.List;

public class Dom4jParseUserXmlTest {
    public static void main(String[] args) {
        //创建解析器对象
        SAXReader saxReader=new SAXReader();
        try {
            Document document = saxReader.read(Dom4jParseUserXmlTest.class.getClassLoader().getResource("users.xml"));
            Element rootElement = document.getRootElement();
            System.out.println("1.------->users.xml文件的根节点的名字是:"+rootElement.getName());

            System.out.println("2.------->获取根标签users的子标签列表");
            List<Element> usersSubElementList = rootElement.elements();
            for (Element userElement : usersSubElementList) {
                System.out.println("users标签的子标签的名字是"+ userElement.getName());
                System.out.println("users标签的子标签的id属性值是"+ userElement.attributeValue("id"));
                System.out.println("users标签的子标签的country属性值是"+ userElement.attributeValue("country"));
                System.out.println("3.------->获取user的子标签列表");
                List<Element> userSubElementList = userElement.elements();
                for (Element userSubElement : userSubElementList) {
                    System.out.println("user标签下的子标签名为:"+userSubElement.getName());
                    System.out.println("user标签下的子标签文本是:"+userSubElement.getText());
                }
            }
            //获取users标签的第一个user标签
            Element firstUserElement = rootElement.element("user");
            //第一个user标签的子标签password的文本内容
            String password = firstUserElement.attributeValue("password");
            System.out.println(password);
        } catch (DocumentException e) {
            e.printStackTrace();
        }
    }
}
```



### 6.3 DOM4J集成XPath

- Dom4j提供基于XPath的API

> Node selectSingleNode(String xpathExpression);	根据XPath表达式获取单个标签(元素/节点)
> List < Node > selectNodes(String xpathExpression)	根据XPath表达式获取多个标签(元素/节点)



### 6.4 XPath语法

[w3school 在线教程](https://www.w3school.com.cn/)



- 测试

```java
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.Node;
import org.dom4j.io.SAXReader;

import java.util.List;

public class Dom4jXPathParseUserXmlTest {
    public static void main(String[] args) {
        SAXReader saxReader=new SAXReader();
        try {
            Document document= saxReader.read(Dom4jXPathParseUserXmlTest.class.getClassLoader().getResource("users.xml"));
            //拿到第一个用户的密码
            System.out.println("1.------>使用绝对路径方式来查找元素");
            Element element = (Element) document.selectSingleNode("/users/user/password");
            String password = element.getText();
            System.out.println(password);

            System.out.println("2.------>使用相对路径查找元素");
            //element是当前获取的password元素
            Element name = (Element) element.selectSingleNode("../name");
            System.out.println("第一个用户的姓名为"+name.getText());

            System.out.println("3.------>使用全局搜索的方式");
            //获取所有的id元素的文本
            List<Node> idNodeList = document.selectNodes("//id");
            for (Node node : idNodeList) {
                Element idElement=(Element) node;
                System.out.println(idElement.getText());
            }
            System.out.println("4.------>谓语形式");
            //获取id=10002的用户信息
            Element idElement = (Element) document.selectSingleNode("//user[@id='10002']");
            List<Element> elements = idElement.elements();
            for (Element element1 : elements) {
                System.out.println(element1.getName()+"="+element1.getText());
            }
        } catch (DocumentException e) {
            e.printStackTrace();
        }
    }
}
```

