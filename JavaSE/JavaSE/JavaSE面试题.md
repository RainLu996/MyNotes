# JavaSE面试题

## 一、Java基础篇

### 1. 什么是面向对象？

​		面向对象是一种思想，是一种处理问题的角度。它是基于面向过程的，是对面向过程的优化。面向过程更注重于完成事情的每一个**步骤及顺序**，而面向对象更注重于完成事情会有哪些参与者（对象），以及这些对象各自需要做些什么。**面向过程比较直接高效，而面向对象更易于复用、扩展和维护。**



> **扩展知识------->归纳、阐述面向对象中的关键术语：**
>
> 类：不存在的，人类大脑思考总结的一个模板（这个模板当中描述了共同特征）；
>
> 对象：实际存在的个体；
>
> 实例：对象的另一个别称；
>
> 实例化：通过类这个模板创建对象的过程，叫做：实例化；
>
> 抽象：多个对象具有共同特征，进行思考总结抽取共同特征的过程。
>
> 
>
> **<font color="red">类--【实例化】-->对象（实例）</font>**
>
> **<font color="red">对象--【抽象】-->类</font>**



### 2. 什么是OOP（面向对象编程）？

​		在实现项目功能时，需要先分析完成这些功能需要哪些对象的参与，这些对象的关系是什么？再根据这些对象的共同特征来抽象出一个个的类，并在类中定义属性、方法。一切皆对象，由对象之间的分工合作来实现项目功能。



### 3. 面向对象三大特征

> ​		封装、继承、多态。

- 封装

  ​	封装给对象提供了**隐藏内部特性和行为**的能力。对象可以封装内部实现，而对外提供一些接口来改变它内部的数据。

- 继承

  ​	继承给对象提供了**从父类获取字段和方法**的能力，提高了代码的复用性。通过继承，可以在不修改类的情况下给类扩展新的功能。

- 多态

  ​	**多态可以降低程序的耦合度，提高程序的扩展性**。多态是指**在父类中定义的属性和方法被子类继承之后，可以具有不同的数据类型或表现出不同的行为**。这使得同一个属性或方法在父类及其各个子类中具有不同的含义。即，多态就是同一个行为具有多个不同表现形式或形态的能力。

- 抽象（了解）

  ​	抽象是**把多个类或对象的共同特征分离出来的步骤**，是根据它们的***<font color="blue">功能</font>***而不是***实现细节***来创建类。Java支持创建只提供接口定义而不包含方法实现的抽象类，<u>它的作用就是提取多个类的共同特征</u>。所以，抽象类本身并不存在，也就无法定义对象喽。

> ​		**扩展知识：**
>
> 实现多态的3个必要条件
>
> - **<font color="red">继承：</font>**在多态中必须存在有继承关系的子类和父类。
> - **<font color="red">重写：</font>**子类对父类中的某些方法进行重写，在调用这些方法时就会调用子类的方法。
> - **<font color="red">向上转型：</font>**在多态中需要将子类的引用赋给父类对象，只有这样该引用才既能可以调用父类的方法，又能调用子类的方法。（如果需要使用子类中特有的方法，只能先向下转型为子类对象）



> ​	有关多态的疑问：
>
> **降低耦合度是如何体现的**？
>
> ```java
> // Master和Dog以及Cat的关系很紧密（耦合度高）。导致程序的扩展性很差
> public class Master{
>     public void feed(Dog d){}
>     public void feed(Cat c){}
> }
> 
> /* 一番改进-------------------------------- */
> // Master和Dog以及Cat的关系脱离了，Master关注的是Pet类。这样Master和Dog以及Cat的耦合度就降低了，也就提高了软件的扩展性。（只要是Pet的子类，都能满足使用条件）
> public class Master{
>     public void feed(Pet pet){}
> }
> ```



### 4. 面向对象和面向过程的区别

**面向过程**

- 优点：单片机、嵌入式开发、 Linux/Unix等一般采用面向过程开发，性能是最重要的因素。

- 缺点：耦合度高、扩展性差，没有面向对象易维护、易复用、易扩展。



**面向对象**

- 优点：***易维护、易复用、易扩展。***由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统更加灵活、更加易于维护。

- 缺点：性能比面向过程低。



### 5. 访问修饰符的作用范围

访问的范围大小：private < default，缺省 < protected < public 

|    修饰符     | 同类   |  同包  | 同包子类 | 不同包子类 | 任意位置 |
| :-----------: | :--: | :----: | :--------: | :--------: | :------: |
|    private    | 可以 | 不可以 | 不可以 |   不可以   |  不可以  |
| default(缺省) | 可以 |  可以  | 可以 |   不可以   |  不可以  |
|   protected   | 可以 |  可以  | 可以 |    可以    |  不可以  |
|    public     | 可以 |  可以  | 可以 |    可以    |   可以   |

1、private（私有）：private修饰的属性和方法，不能被其它类访问，也不能被子类继承和访问，只能在当前类访问。

2、default （缺省）：没有加修饰符的属性和方法，同一个包的其他类可以访问和继承。

3、protected（受保护的）：被其修饰的属性和方法，同一个包的其他类可访问和继承，或者不同包的其他子类可访问。

4、public（公有的）：不存在访问权限，全部类都可以访问。



### 6. 接口和抽象类的区别

- **语法层面上的区别：**
    - 抽象类可以提供成员方法的实现细节，而接口中只能存在 public abstract 方法;
    - 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 public static final 类型的;

    - 抽象类可以有静态代码块和静态方法，而接口中可以含有静态方法但是不能含有静态代码块（JDK8新特性）；
    - 一个类只能继承一个抽象类，而一个类却可以实现多个接口。



- **设计层面上的区别：**
    - 抽象类是对一种事物的抽象，即对类抽象，而接口是对行为的抽象。抽象类是对整个类整体进行抽象，包括属性、行为，但是接口却是对类局部（行为）进行抽象。
    - 设计层面不同，抽象类作为很多子类的父类，它是一种`模板式设计`。而接口是一种行为规范，它是一种`辐射式设计`。



### 7. 值传递和引用传递的区别是什么？为什么说Java中只有值传递?

- `值传递`：指的是在方法调用时，传递的参数是按值的拷贝传递，**传递的是值的拷贝**，**也就是说传递后就互不相关了**。

- `引用传递`：指的是在方法调用时，传递的参数是按引用进行传递，其实传递的是引用的地址，也就是变量所对应的内存空间的地址。**传递的是值的引用，也就是说传递前和传递后都指向同一个引用(也就是同一个内存空间)。传递后仍然相关联**。

关键的区别即为：

**1）调用方法时有没有对实参进行复制。**

**2）方法内对形参的修改会不会影响到实参。**



> ​		Java 的确是值传递的。只不过，引用数据类型在调用有参方法的时候，传递的是**<font color="red">对象引用值的拷贝</font>**，而并不是对象本身。即使对象本身发生了变化，但对象的引用在传递的过程中并没有发生改变。



### 8. 深拷贝、浅拷贝、引用拷贝的区别

#### 1）引用拷贝

|                           引用拷贝                           |
| :----------------------------------------------------------: |
| ![image-20230130154649208](imgs\image-20230130154649208.png) |

​		引用拷贝不会在`堆`中创建一个新对象，只会在`栈`中生成一个新的引用地址，最终指向的依然是**堆中的同一个对象**。

---

- Demo

```java
public class TestForRefCopy {

    public static void main(String[] args) {
        Person p1 = new Person("小张"， 180， new StringBuilder("今天天气很好"));
        Person p2 = p1;

        System.out.println("对象是否相等：" + (p1 == p2));
        System.out.println("p1 属性值=" + p1.getName() + "，" + p1.getHeight() + "，" + p1.getSomething());
        System.out.println("p2 属性值=" + p2.getName() + "，" + p2.getHeight() + "，" + p2.getSomething());


        // change
        p1.name = "小王";
        p1.height = 200;
        p1.something.append("，适合出去玩");
        System.out.println("...after p1 change....");

        System.out.println("p1 属性值=" + p1.getName() + "，" + p1.getHeight() + "，" + p1.getSomething());
        System.out.println("p2 属性值=" + p2.getName() + "，" + p2.getHeight() + "，" + p2.getSomething());

    }
}

//实体类
class Person {
    public String name;//姓名
    public int height;//身高
    public StringBuilder something;

    // get & set

    public Person(String name， int height， StringBuilder something) {
        this.name = name;
        this.height = height;
        this.something = something;
    }
}

---------------------------------- 结果
对象是否相等：true
p1 属性值=小张，180，今天天气很好
p2 属性值=小张，180，今天天气很好
...after p1 change....
p1 属性值=小王，200，今天天气很好，适合出去玩
p2 属性值=小王，200，今天天气很好，适合出去玩    
```



#### 2）浅拷贝

|                            浅拷贝                            |
| :----------------------------------------------------------: |
| ![image-20230130154543270](imgs\image-20230130154543270.png) |

​		浅拷贝会在`堆`中创建一个新对象，并进行**属性复制**。这里的属性复制，对于`基本数据类型`来说，复制的是基本数据类型的值；对于`引用数据类型`来说，复制的是引用数据类型变量的地址值。

---

- Demo
    - 实现`Cloneable`接口，以向`Object.clone()`方法指示该方法为该类实例的字段副本创建字段是合法的。
    - 未实现`Cloneable`接口的实例调用`Object.clone()`方法会导致抛出异常：`CloneNotSupportedException`。

```java
public class TestShallowCopy {
    public static void main(String[] args) throws CloneNotSupportedException {
        Person p1 = new Person("小张"， 180， new StringBuilder("今天天气很好"));
        Person p2 = p1.clone();

        System.out.println("对象是否相等：" + (p1 == p2));
        System.out.println("p1 属性值=" + p1.getName() + "，" + p1.getHeight() + "，" + p1.getSomething());
        System.out.println("p2 属性值=" + p2.getName() + "，" + p2.getHeight() + "，" + p2.getSomething());


        // change 
        p1.setName("小王");
        p1.setHeight(200);
        p1.getSomething().append("，适合出去玩");
        System.out.println("...after p1 change....");

        System.out.println("p1 属性值=" + p1.getName() + "，" + p1.getHeight() + "，" + p1.getSomething());
        System.out.println("p2 属性值=" + p2.getName() + "，" + p2.getHeight() + "，" + p2.getSomething());

    }
}

class Person implements Cloneable {
    public String name;//姓名
    public int height;//身高
    public StringBuilder something;

    // get & set

    public Person(String name， int height， StringBuilder something) {
        this.name = name;
        this.height = height;
        this.something = something;
    }

    // 将clone方法改为public的，使得此方法可以在任意地方被调用
    @Override
    public Person clone() throws CloneNotSupportedException {
        return (Person) super.clone();
    }
}

---------------------------------- 结果
对象是否相等：false
p1 属性值=小张，180，今天天气很好
p2 属性值=小张，180，今天天气很好
...after p1 change....
p1 属性值=小王，200，今天天气很好，适合出去玩
p2 属性值=小张，180，今天天气很好，适合出去玩    
```

**注意**：

> ​		String类型，不可变，每当对其进行修改时，JVM会默认在堆上创建新的内存空间，再重新赋值。



#### 3）深拷贝

|                            深拷贝                            |
| :----------------------------------------------------------: |
| ![image-20230130154836944](imgs\image-20230130154836944.png) |

​		深拷贝完全复制整个对象，包括引用类型的属性。	

---

- 方式一：对浅拷贝中的`clone`方法进行一点改进
    - 如果重写了`clone`方法，则不需要实现`Cloneable`接口

```java
@Override
public Person clone() throws CloneNotSupportedException {
    //return (Person) super.clone();
    Person person = (Person) super.clone();
    person.setSomething( new StringBuilder(person.getSomething()));//单独为引用类型clone
    return person;
}
```

​		显而易见，这样有个小问题：对象每有一个引用类型，我们都得重写其clone方法，这样会非常麻烦，因此我们还可以借助**序列化**来实现对象的深拷贝。



- 方式二：借助序列化实现对象的深拷贝
    - 用到了序列化，则需要实现`Serializable`接口
    - 由于重写了clone方法，则不需要实现`Cloneable`接口

```java
//实体类---需要实现Serializable接口
class Person implements Serializable{
    public String name;//姓名
    public int height;//身高
    public StringBuilder something;

    // get & set

    public Object deepClone() throws Exception{
        // 序列化
        // 字节数组输出流，不用跟硬盘交互，直接内存操作，节省了资源和缩短了响应时间
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bos);

        oos.writeObject(this);

        // 反序列化
        ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bis);

        return ois.readObject();
    }

}

public class TestDeepCopy {

    public static void main(String[] args) throws Exception {
        Person p1 = new Person("小张"， 180， new StringBuilder("今天天气很好"));
        Person p2 = (Person)p1.deepClone();

        System.out.println("对象是否相等："+ (p1 == p2));
        System.out.println("p1 属性值=" + p1.getName()+ "，"+ p1.getHeight() + "，"+ p1.getSomething());
        System.out.println("p2 属性值=" + p2.getName()+ "，"+ p2.getHeight() + "，"+ p2.getSomething());


        // change
        p1.setName("小王");
        p1.setHeight(200);
        p1.getSomething().append("，适合出去玩");
        System.out.println("...after p1 change....");

        System.out.println("p1 属性值=" + p1.getName()+ "，"+ p1.getHeight() + "，"+ p1.getSomething());
        System.out.println("p2 属性值=" + p2.getName()+ "，"+ p2.getHeight() + "，"+ p2.getSomething());

    }
}

---------------------------------- 结果
对象是否相等：false
p1 属性值=小张，180，今天天气很好
p2 属性值=小张，180，今天天气很好
...after p1 change....
p1 属性值=小王，200，今天天气很好，适合出去玩
p2 属性值=小张，180，今天天气很好    
```



### 9. JVM是如何处理异常的

​		在一个方法中如果发生异常，这个方法会创建一个异常对象，并转交给JVM。该异常对象包含异常名称、异常描述、以及异常发生时应用程序的状态。**创建异常对象并转交给JVM的过程称为抛出异常。**可能有一系列的方法调用，最终才进入抛出异常的方法，这一系列方法调用的有序列表叫做`方法调用栈`。

​		JVM会顺着调用栈去查找看是否有可以处理异常的代码，如果有，则调用异常处理代码。当JVM发现可以处理异常的代码时，会把发生的异常传递给它。如果JVM没有找到可以处理该异常的代码块，JVM就会将该异常转交给默认的异常处理器（默认处理器为JVM的一部分），默认异常处理器打印出异常信息并终止应用程序。



### 10. Java是编译型语言还是解释型语言

> ​		Java既是编译型语言，也是解释型语言。

------

1. 你可以说它是编译型的。因为所有的Java代码都需要经过javac编译为.class文件，但主要是由于java现在拥有了`JIT`，会将热点代码的.class文件直接编译为二进制本地代码。
2. 你可以说它是解释型的。因为java代码编译后的.class文件还是不能直接运行在操作系统上，还需要经过jvm解释为二进制代码。
3. 现在的JVM为了效率，都有一些**JIT（Just In-Time Compiler）优化**。它又会把热点代码的.class的二进制代码编译为本地的代码直接运行，所以，又是编译的。像C、C++ 他们经过一次编译之后直接可以编译成操作系统了解的类型，可以直接执行的 所以他们是编译型的语言。没有经过第二次的处理 而Java不一样他首先由编译器编译成.class类型的文件，这个是java自己类型的文件 然后再通过虚拟机(JVM)从.class文件中**读一行解释一行；执行一行**，所以它是解释型的语言。**而由于java对于多种不同的操作系统有不同的JVM定制，并且字节码文件的生成格式在任何的操作系统中都是一样的。所以 Java实现了真正意义上的跨平台！**

------

- **编译型语言与解释型语言的定义**
  - 编译型语言：将源程序全部编译成**<font color="green">二进制代码</font>**的可运行程序。然后，可直接运行这个程序。
  - 解释型语言：将源程序翻译一句，然后执行一句，直至结束！



- **编译型语言与解释型语言的区别**
  - 编译型语言：执行速度快、效率高；依靠编译器、跨平台性差些。
  - 解释型语言：执行速度慢、效率低；依靠解释器、跨平台性好。

> #### 									编译型语言与解释型语言的跨平台性差别
>
> ​		计算机只能识别二进制指令，不能直接识别由JavaScript等高级编程语言所编写的代码（源代码），所以需要将高级语言转为二进制指令。
>
> ​		由于不同语言转换为二进制指令的时机不同，可分为**编译型语言**和**解释性语言**。
>
> #### 一、编译型语言
>
> - 编译型语言
>
>   ​		编译型语言要求使用编译器一次性将所有源代码编译为一个**可执行程序（即：二进制代码）**，一次编译可重复执行。代表语言有C、C++、Golang、汇编等。
>
> - 编译型语言一般不能跨平台
>
>   - **编译出来的可执行程序不能跨平台：**因为不同操作系统对可执行文件有着不同的要求，彼此之间不能兼容。
>   - **源代码不能跨平台：**不同操作系统下的函数、变量、api等可能会有不同。
>
> #### 二、解释型语言
>
> - 解释型语言
>
>   ​		解释型语言是使用解释器将源代码翻译一句执行一句，不会一次性生成一个二进制的可执行程序。
>
> - 解释型语言一般可以跨平台
>
>   ​		跨平台是指源代码可以跨平台，解释器是不能跨平台的。源代码在不同操作系统中运行的结果相同。



- 了解：哪些是编译型语言与解释型语言
  - 编译型的语言包括：C、C++、Delphi、Pascal、Fortran
  - 解释型的语言包括：Basic、javascript



### 11. 什么是hashcode()

​		hashCode() 的作用是获取哈希码，也称为散列码；实际上就是返回一个int整数。这个哈希码的作用是确定当前对象在`哈希表`中的**索引位置**。hashCode() 定义在Object类中，这就意味着Java中的任何类都包含有hashCode()函数。散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以利用散列码快速找到所需要的对象）



### 12. 为什么要设计hashCode?

​		以“HashSet 如何检查重复”为例来说明为什么要设计 hashCode：

​		当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其它已经加入的对象的 hashcode 值作比较，如果在hash表中不存在相同的hashcode，HashSet会假设对象没有重复出现。但如果发现有相同 hashcode 值的对象，这时会调用 equals()方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就**大大减少了 equals 的次数，也就大大提高了程序执行速度**。

> ​	所以，就是为了减少进行equals的次数，提高程序的执行速度。



### 13. hashCode()与equals()方法的关系

- 如果两个对象equals结果为true，Java 运行时环境会认为他们的 hashCode 一定相等；
- 如果两个对象equals结果为false，则他们的 hashCode 有可能相等；
- 如果两个对象的hashCode相等，他们不一定equals；
- 如果两个对象的hashCode不相等，他们一定不equals



### 14. 为什么重写equals方法时必须重写hashcode方法

​		在准备将对象加入哈希表中，进行加入位置判断的时候，**程序先根据hashcode进行判断，若hashcode相同，再根据equals()方法进行判断**。此时，如果只重写了equals方法，而不重写hashcode的方法，可能会造成hashcode的值不同，而equals()方法判断出来的结果为true的情况。而在Java中的一些容器中，不允许存在有两个完全相同的对象，会在插入的时候，如果判断两个对象相同则进行覆盖。这个时候就有可能因为没有重写hashcode方法，造成相同的对象散列到不同的位置而造成**对象的不能覆盖**的问题。

> ​		Object中的hashcode是**根据对象的存储地址转换而形成的一个哈希值**。



### 15. 字符串常量池与整数型常量池





### 16. Integer和int的区别&为什么要设计包装类型

- 它们作为**成员变量**（局部变量必须要赋值才能通过编译）时，Integer的默认值为null，而int的默认值为0；
- Integer必须要实例化之后才能使用，而int不需要，直接存储数值；
- Integer存储在堆内存中，而int类型是直接存储在栈空间中；
- Integer是一个对象类型，它封装了许多属性和方法，在使用的时候会更加的灵活。

> ​		至于为什么要设计包装类型，我认为是因为Java本身是一个面向对象的语言，一切操作都是以对象为基础的。比如说往集合中存储元素，只支持存储Object类型的数据，而普通类型是无法通过集合来存储的。



### 17. 基本数据类型与包装类型之间的比较

​		例如：Integer变量和int变量比较时，只要两个变量的**值**是相等的，则结果为true。因为包装类Integer和基本数据类型int比较时，java会自动拆包装为int，然后进行比较，**实际上就变为两个int变量的比较**。



### 18. 什么是反射？使用反射有什么优缺点？

​		反射是在运行状态中，对于任意一个类，都能够知道这个类中的所有属性和方法；对于任意一个对象，都能够调用它的任意一个属性和方法；这种**在程序运行时，动态获取类信息以及动态调用对象方法的功能就称为反射机制**。

- **优点**：
    - 能够在程序运行时动态获取类的实例，提高灵活性；
    - 它允许程序创建和控制任何类的对象，而无需提前**硬编码**目标类，降低程序的耦合度。
- **缺点**：
    - 使用反射的性能较低，需要解析字节码，将内存中的对象进行解析。其解决方案是：通过setAccessible(true)关闭JDK的安全检查来提升反射速度；
    - 多次创建一个类的实例时，有缓存会快很多；ReflectASM工具类，通过字节码生成的方式加快反射速度。



### 19. 获取Class对象的方式

- `Class.forName(“类的路径”);`：当你知道该类的全限定名称时，就可以使用该方法获取 Class类对象。

```java
Class clazz = Class.forName("java.lang.String");
```



- `类名.class`：这种方法只适合在编译前就知道操作的 Class。

```java
Class clazz = String.class;
```



- `对象名.getClass()`：这种方法只适合在编译前就知道操作的 Class。

```JAVA
String str = new String("Hello");
Class clazz = str.getClass();
```



- 如果是基本类型的包装类，可以调用包装类的`TYPE`属性来获得该包装类对应的基本类型的Class对象。

```java
Class clazz = int.class;
Class clazz = Integer.TYPE;
```



### 20. 基本数据类型的Class对象

​		有9个预先定义好的Class对象代表着**8个基本数据类型和void。**它们被Java虚拟机创建，和基本数据类型有相同的名字。

```java
System.out.println(int.class);    // 输出int
System.out.println(float.class);  // 输出float
System.out.println(char.class);   // 输出char
System.out.println(void.class);   // 输出void
……
```



​		这8个基本类型的Class对象可以通过java.lang.Boolean.TYPE；java.lang.Integer.TYPE等来访问。也可以通过int.class；boolean.class等来访问。但是**Integer.class并不等价于int.class！！！**如下所示：

```java
System.out.println(Integer.TYPE == int.class);     // true
System.out.println(Integer.class == int.class);    // false
```



**总结**：

> ​		int.class与Integer.TYPE是等价的，但是与Integer.class是不相等的。因为int.class或者Integer.TYPE指的是int的Class对象，而Integer.class指的是Integer的Class对象。



### 21. Java中为什么引入反射？反射机制的应用有哪些？

官方解答：反射机制的应用场景

- 反射让开发人员可以通过**外部类**的全限定名创建对象，并能使用这些类，实现一些扩展的功能。
- 反射让开发人员可以枚举出类的全部成员，包括构造函数、属性、方法。以帮助开发者写出正确的代码。
- 测试时可以利用反射 API 访问类的私有成员，以保证测试代码的覆盖率。



> ​		也就是说，之所以会引入反射机制，是因为我们可以将反射作为一个工具，用来帮助我们实现本不可能实现的功能。例如在JDBC中进行数据库连接，在注册驱动的时候，如果使用的是原始的new驱动对象的方式注册驱动，则会导致驱动被注册两次，并过度依赖于当前数据库驱动的api，一旦脱离的对当前数据库的开发包，程序则无法编译执行。此时，利用反射，我们可以使用对应驱动的Class类来注册驱动，具体就是利用反射的`Class.forName(驱动的全限定类名);`方法。如果将驱动作为配置文件的配置项进行配置，这样，在有更换数据库的需求的时候，我们直接更换配置文件中的驱动名称即可，再将jar包更换。而不需要重新编译运行代码，这降低了程序的耦合性，提高了程序的扩展性。





### 22. 反射的原理

56

确定一个对象的类

--》取出类的修饰符modifiers、属性、方法、构造器、父类/接口

--》找出某个接口里定义的常量和方法信息

--》创建一个类实例，这个实例在运行时刻才有名字(运行时间才生成的对象)

--》取得和设定对象数据成员的值，如果数据成员名是运行时刻确定的也能做到

--》在运行时刻调用动态对象的方法

--》创建数组，数组大小和类型在运行时刻才确定，也能更改数组成员的值



### 23. Java中的I/O流分类

[IO流：节点流和处理流详细归纳。节点流和处理流的区别](https://blog.csdn.net/m0_59092234/article/details/126032376)

- 按照`流的方向`进行分类（将内存作为参照物）：
  - 写入内存中去，叫做：输入（Input）/ 读（Read）
  - 从内存中出来，叫做：输出（Output）/ 写（Write）
- 按照`处理数据的单位`进行分类：
  - 有的流读取**字节**：一次读取一个字节。这种流是万能的，可以读取任意类型的文件。例如：文本文件（例：word文件）、图片、音频、视频......
  - 有的流读取**字符**，一次读取一个字符。这种流是为了方便读取普通文本文件而存在的，而对于其它类型的数据则无法读取。但是字符流处理文本比字节流处理文本要方便。【**在读写文本文件并且需要对文件内容做一些特殊处理的，用字符流；但只是读写文本文件，而不需要对文件内容进行一些操作的，用字节流**】这种流不能读取：图片、音频、视频......而只能读取纯（普通）文本文件

- 按照`流的功能`进行分类：
  - `节点流`：可以从或向一个特定的地方读写数据，如 FileReader
  - `处理流`：是对一个已存在的流的连接和封装，通过所封装的流的功能调用实现数据读写，如BufferedReader



### 24. 什么是阻塞I/O？什么是非阻塞I/O？

​		IO操作包括：对硬盘的读写、对socket的读写以及外设的读写。

​		当用户线程发起一个IO请求操作（本文以读请求操作为例），内核会去查看要读取的数据是否就绪，对于`阻塞IO`来说，如果数据没有就绪，则会一直在那等待，直到数据就绪；对于`非阻塞IO`来说，如果数据没有就绪，则会返回一个标志信息告知用户线程当前要读的数据没有就绪。当数据就绪之后，便将数据拷贝到用户线程，这样才完成了一个完整的IO读请求操作。

​		也就是说一个完整的IO读请求操作包括两个阶段：

​		1）查看数据是否就绪；

​		2）进行数据拷贝（内核将数据拷贝到用户线程）。

​		`阻塞（blocking IO）`和`非阻塞（non-blocking IO）`的区别就在于第一个阶段，如果数据没有就绪，在查看数据是否就绪的过程中是一直等待，还是直接返回一个标志信息。

​		**Java中传统的IO都是阻塞IO**。比如通过socket来读数据，调用read()方法之后，如果数据没有就绪，当前线程就会一直阻塞在read方法调用那里，直到有数据才返回；而如果是非阻塞IO的话，当数据没有就绪，read()方法应该返回一个标志信息，告知当前线程数据没有就绪，而不是一直在那里等待。

[想理解Java的IO，不要从操作系统开始说起的都是耍流氓... (qq.com)](https://mp.weixin.qq.com/s/p5qM2UJ1uIWyongfVpRbCg)



### 25. BIO、NIO、AIO的区别

​		`BIO`：**同步并阻塞**。在服务器中实现的模式为**一个连接一个线程**。也就是说，客户端有连接请求的时候，服务器就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然这也可以通过线程池机制改善。BIO一般适用于**连接数目小且固定的架构**，这种方式对于服务器资源要求比较高，而且并发局限于应用中，是JDK1.4之前的唯一选择，但好在**程序直观简单，易理解**。

​		`NIO`：**同步并非阻塞**。在服务器中实现的模式为**一个请求一个线程**，也就是说，客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到有连接IO请求时才会启动一个线程进行处理。NIO一般适用于**连接数目多且连接比较短（轻操作）的架构**，并发局限于应用中，**编程比较复杂**，从JDK1.4开始支持。

​		`AIO`：**异步并非阻塞**。在服务器中实现的模式为**一个有效请求一个线程**，也就是说，客户端的IO请求都是通过操作系统先完成之后，再通知服务器应用去启动线程进行处理。AIO一般适用于**连接数目多且连接比较长（重操作）的架构**，充分调用操作系统参与并发操作，**编程比较复杂**，从JDK1.7开始支持。



## 二、Java集合篇

### 1. 常见的集合有哪些

​		Java集合类主要是由两个根接口`Collection`和`Map`派生出来的。Collection派生出了三个子接口：List、Set、Queue（Java5新增的队列），因此Java集合大致也可分成List、Set、Queue、Map四种接口体系。

> ​		注意：`Collection`是一个接口，`Collections`是一个工具类，**Map不是Collection的子接口**。



|                   Collection接口体系-常见                    |
| :----------------------------------------------------------: |
| ![image-20230201204141169](imgs\image-20230201204141169.png) |



|                       Map接口体系-常见                       |
| :----------------------------------------------------------: |
| ![image-20230201204231669](imgs\image-20230201204231669.png) |



- `List`代表**有序可重复集合**，可直接根据元素的索引来访问；
- `Set`代表**无序不可重复集合**，只能根据元素本身来访问；
- `Queue`代表**队列集合**；
- `Map`代表的是**存储key-value键值对的集合**，可根据元素的key来访问value。

​		上图中淡绿色背景覆盖的是集合体系中常用的实现类，分别是ArrayList、LinkedList、ArrayQueue、HashSet、TreeSet、HashMap、TreeMap等实现类。



### 2. ArrayList和LinkedList的异同点

- **是否保证线程安全**：ArrayList 和 LinkedList 都是不同步的，也就是不保证线程安全；
- **底层数据结构**： ArrayList底层使用的是`Object数组`；LinkedList 底层使用的是`双向循环链表`数据结构；
- **是否支持快速随机访问**： LinkedList不支持高效的随机元素访问，而ArrayList 实现了`RandomAccess` 接口，所以有随机访问功能。<u>快速随机访问就是通过元素的序号快速获取元素对象</u>（即`get(index)`方法）。
- **内存空间占用**： ArrayList的空间浪费主要体现在在list列表的结尾会**预留一定的容量空间**，而LinkedList的空间花费则体现在它的**每一个元素都需要消耗比ArrayList更多的空间**（因为要存放直接后继和直接前驱以及数据）。
- **插入和删除的性能问题**：<u>如果是将元素进行尾插</u>（使用`add(ele);`），使用ArrayList时会存在扩容时带来的性能损耗，而LinkedList不存在此问题，所以在这种情况下一般推荐使用LinkedList；<u>如果是将元素插入到指定位置</u>（`add(index， ele)`），就需要具体考虑了。因为ArrayList会进行元素移位的操作，并且还存在着数组扩容的问题。LinkedList需要遍历链表，再进行数据插入。这两种到底哪种更快，是没有结论的，得具体情况具体分析。删除操作就大同小异了……
- **查询数据时的性能问题**：查询分两种，第一种就是`普通遍历`，也就是对List中的元素进行遍历，本质就是遍历链表和遍历数组的区别，那么这两种是区别不大的；第二种就是经常需要`按指定下标获取集合中的元素`，如果这种情况比较多，就用ArrayList，因为ArrayList支持随机访问，效率更高。使用LinkedList需要从头遍历到指定下标处，再返回下标处的元素，效率较低。



### 3. 为什么遍历ArrayList使用for循环比Iterator快，而LinkedList中却是Iterator远快于for？

​		因为ArrayList中每个元素的下标是明确的，使用for循环根据元素下标获取（`get()`）元素的时间复杂度仅为O(1)。而LinkedList中每个元素是按照双向链表的形式排列在一起的，如果我们使用for循环遍历：那我`get(0)`获取第一个节点，`get(10)`则它要从第一个节点开始一个一个往下找直到找到第十个节点，以此类推。因此很浪费时间。当使用迭代器时，它是每取一个元素就把**游标**指向下一个节点，根据游标就可以得到下一个元素，因此不用像for循环一样那么耗时（迭代器就是在使用集合获取对应迭代器的**当前集合状态**）。



### 4. ArrayList和Vector的区别

- Vector是线程安全的，ArrayList不是线程安全的。其中，Vector在关键性的方法前面都加了synchronized关键字，来保证线程的安全性。如果有多个线程会访问到集合，那最好是使用 Vector，因为不需要我们自己再去考虑和编写线程安全的代码。
- 在进行数组扩容时，ArrayList是在原来的基础上扩展`0.5倍`，Vector是扩展`1倍`，这样ArrayList就有利于节约内存空间。



### 5. ArrayList的扩容机制

​		我们知道，ArrayList本质上是一个`数组结构`的存储容器，**默认情况下，数组长度是10**。当然，我们也可以在创建ArrayList对象时指定数组的初始化长度。随着不断的向ArrayList中添加数据，当添加的数据超过了数组的长度时，将会触发ArrayList的自动扩容机制。扩容的流程其实很简单，就是先计算出新的扩容数组的size后再实例化，然后将原数组中的所有元素全部复制到新扩容的数组中来。默认情况下，新数组的容量大约是原数组容量的`1.5倍`。

> ​		有个小细节：ArrayList初始化后，如果没有存放任何数据，则数组长度为0；只有当向ArrayList中添加数据之时，数组容量才会是默认容量10。



### 6. HashMap的底层数据结构

​		在JDK1.7 和JDK1.8 中有所差别：在`JDK1.7` 中，由“**数组+链表**”组成，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的。

​		在`JDK1.8` 中，由“**数组+链表+红黑树**”组成。当链表过长，则会严重影响 HashMap 的性能，红黑树搜索时间复杂度是 O(logn)，而链表是糟糕的 O(n)。因此，JDK1.8 对数据结构做了进一步的优化，引入了红黑树，链表和红黑树在达到一定条件会进行转换：

- **当链表长度超过8且数据总量超过64，链表会转为红黑树**；
- **如果链表长度超过8，但是当前数组的长度小于64，则会进行数组扩容，而不是转换为红黑树，以减少搜索时间**。

|                     HashMap底层数据结构                      |
| :----------------------------------------------------------: |
| ![image-20230202093522750](imgs\image-20230202093522750.png) |



### 7. 解决hash冲突的办法有哪些？HashMap用的哪种？

> ​		解决Hash冲突的方法有：**开放定址法**、**再哈希法**、**链地址法（拉链法）**、**建立公共溢出区**。HashMap中采用的是`链地址法`。

- **开放定址法|线性探测法**：直接从发生冲突的数组位置顺着往下寻找一个空的数组下标位置进行数据存储。
- **再哈希法（双重散列，多重散列）**：提供多个不同的hash函数，当R1=H1(key1)发生冲突时，再计算R2=H2(key1)，直到没有冲突为止。比如`布隆过滤器`就使用到了这种方法。
- **链地址法（拉链法）**：将哈希值相同的元素构成一个同义词的单链表，并将单链表的头指针存放在哈希表数组的第i个单元中，查找、插入和删除主要在同义词链表中进行。链表法适用于经常进行插入和删除的情况。
- **建立公共溢出区**：将哈希表分为`公共表`和`溢出表`，当溢出发生时，将所有溢出数据统一放到溢出区。



### 8. 为什么在解决hash冲突的时候，不直接用红黑树？而选择先用链表，再转红黑树?

​		因为红黑树需要进行**左旋，右旋，变色**这些操作来保持平衡，而单链表不需要。当`元素小于8个`的时候，此时做查询操作，链表结构已经能保证查询性能。当`元素大于8个`的时候，红黑树搜索的时间复杂度是 O(log<sub>n</sub>)，而链表是 O(n)，此时需要红黑树来加快查询速度，但是新增节点的效率变慢了。因此，**如果一开始就用红黑树结构，元素太少，新增节点的效率又比较慢，无疑这是浪费性能的**。



### 9. HashMap的扩容机制

​		HashMap在**数组为空**、**存储的数据总量超过最大容量与负载因子的乘积**、**当链表长度大于8且数组长度小于64**时，就会进行扩容。HashMap扩容时会创建一个比原数组大两倍的数组，然后遍历原数组，将原来所有的数据都迁移到新数组中。数据迁移时，需要重新hash，因为数组下标index是根据`hash值 &（length - 1）`计算的，扩容后长度改变，hash值也会随之改变。在JDK1.7中，扩容采用的是头插法，但由于头插法会改变原节点的位置，在多线程的情况下，原先按顺序排列的链表就会出现首尾相连的问题，所以在JDK1.8之后就采用了尾插法。其实扩容操作对于效率的影响是很大的，所以我们在使用HashMap时应该提前计算好需要的大小，尽量避免出现扩容而影响效率。 

[(71条消息) 关于HashMap扩容机制_hashmap的扩容机制_祝枝繁的博客-CSDN博客](https://blog.csdn.net/zhuzhianing/article/details/123962898)



### 10. HashMap默认加载因子是多少？为什么是0.75，不是0.6或者0.8？

- `capacity` 为 Node[] table 【专业术语：哈希桶】的初始化长度/容量为(默认值是16)；
- `size`为HashMap中实际所存放的K-V的数量（为链表和树中的K-V的总和）；
- `Load Factor`为负载因子(默认值是0.75)，表示Hash表中元素的填充程度；
- `threshold`是HashMap进行数组扩容时的阈值（`threshold = capacity * Load factor`），即超过这个值之后，就会进行数组扩容。

> ​		负载因子表示的是hash表中元素的填充程度，负载因子的值越大，那么意味着触发扩容所需要的元素个数会更多，虽然它整体的空间利用率比较高，但是产生hash冲突的概率会增加。反之，扩容因子的值越小，那么触发扩容所需要的元素个数就更少，那么意味着产生hash冲突的概率也会减小，但是，对内存空间的浪费就比较多了，而且还会增加扩容的频率。因此，对负载因子值的设置，本身就是对`发生哈希冲突的概率`与`空间利用率`之间的一个平衡。0.75这个值的来源，和统计学中的`泊松分布`有关。我们知道，HashMap中采用的是`链地址法`来解决hash冲突的问题，而为了避免链表过长带来的一个**时间复杂度增加**的情况，会在链表长度超过8，并且集合中元素个数超过64时，将链表转换为红黑树，从而提升检索的效率。**当负载因子的值为0.75时，链表的长度达到8的可能性很小，几乎为0，也就是说达到了一个比较好的空间成本与时间成本的平衡**。

- 如果内存空间很多而又对时间效率要求很高，可以降低负载因子Load Factor的值。
- 如果内存空间紧张而对时间效率要求不高，可以增加负载因子Load Factor的值。

|           HashMap中数据存储形式Demo           |
| :-------------------------------------------: |
| ![这里写图片描述](imgs\20160407175503308.png) |

> ​		注意：**Load Factor的值可以大于1**。也就是说，如果我们的构造函数传入的 Load Factor大于 1，HashMap 并不会报错，只是会增加发生Hash冲突的概率，降低程序的执行效率。



### 11. HashMap中key的数组存储索引的计算规则

**步骤**：

1. `hashCode(32位)`：HashMap的Key对象中的hashCode方法返回的值；
2. `hash`：基于hashCode的值，使用算法计算得到的hash值；【[二次hash](#12.-HashMap为什么要二次Hash？又为什么要右移16位异或？)】
3. `index`：基于hash值计算产生的，HashMap底层数组Map.Entry[]的偏移值。



> ​		index的计算原则是：**性能高效** 与 **均匀分布**。虽然存在【取模运算】与【位运算】两种计算index的方式，但由于位运算的执行效率更高，所以在HashMap中，使用位运算来计算index。

① **取模运算**

​		如果hash值为int，而index需要映射到0 ～ length -1，最简单直观的就是使用取模运算，也就是：

`index = hash值 % length `。但是，Java 官方没有采用这个办法，因为这种效率不是最高的。



② **位运算**

​		为了解决取模效率的问题，Java官方采用位运算的方法。即：`index = hash值 & （length - 1）`。

这个时候，如果想让index值的范围也是 0 ～ length -1，需要一个前置条件：==length的长度必须是2的n次方==。

​		只有当length的长度是2的n次方时，公式才成立：**num % 2<sup>n</sup> = num & (2<sup>n</sup> - 1)**。因为**一个数num除以2<sup>n</sup>，就相当于右移n位，那么移出去的那些数自然就是余数了**。

| 例子------------>158除以8 |
| :-----------------------: |
| ![img](imgs\hashmap.png)  |



### 12. HashMap为什么要二次Hash？又为什么要右移16位异或？

​		之所以要对key的hashCode值进行二次hash，即`无符号右移16位再异或`，其核心的目的是为了**让hash值的散列度更高，尽可能的去减少hash表的hash冲突，使数据分布的更加均匀，从而去提升数据的查找性能**。如果不存在二次hash，在put时，由于是用`key的hash值`与`数组长度-1`进行的与运算得到的数组位置index，而在大多数业务场景下，数组长度是小于2<sup>16</sup>也就是65536的，所以也就意味着在计算数组位置index时，始终都是用`hash值的低16位`与`数组长度-1`进行与运算（由与运算特征决定：数组长度高位由于都是0，则无论怎样和hash值相&都是0）。这就会导致散列度不是很高，而导致大量的key集中存储在几个固定的数组位置上，很显然会影响到数据的存储性能。因此，为了提升key的hash值的散列度，在hash()方法中又对key的hashCode进行了一个无符号右移16位再异或的运算，这就意味着**让hashCode的高16位也参与了运算**。正是这种将高位和低位的hashCode的特征组合起来所得出的二次hash值，在进一步计算数组下标index时的散列度就会更高。也就达到了降低hash冲突的概率，数据均匀分布的目的。

​		根据`哈希码(hashcode)`计算下标Index的过程，大家也发现了。实际上，只有数组长度以内的hashcode低位才会参与运算。例如数组长度是16，那么只有hashcode低4位会参与计算；如果数组长度是256，那么只有hashcode低8位会参与计算；如果数组长度是65536，那么只有hashcode低16位会参与计算。**HashMap取16位是一个折中的选择**，绝大部分情况下，HashMap数组的长度都不会超过65536。

> ​		异或运算：相同为0，不同为1。



### 13. HashMap的get方法原理

1. 首先根据 key 的值计算 hash 值，并将hash值转换为该元素在数组中存储的下标；
2. 如果下标所对应的位置上没有任何数据，则返回null；
3. 如果这个位置上有数据存在，不管是链表还是红黑树，就将key与其一一进行对比，直到对比出一个相等的key出来，再返回这个key所对应的value。



### 14. HashMap的put方法原理

以JDK1.8为例，简要流程如下：

1. 首先根据 key 的值计算 hash 值，并将hash值转换为该元素在数组中存储的下标；
2. 如果数组是空的，则调用 resize 进行初始化；
3. 如果没有哈希冲突直接放在对应的数组下标里；
4. 如果冲突了，且 key 已经存在，就覆盖掉 value；
5. 如果冲突了，且 key 不存在，发现该节点是红黑树，就将这个节点挂在树上；如果是链表，则判断该链表长度是否大于 8 ，如果大于 8 并且数组长度小于 64，就进行扩容；如果链表节点数大于 8 并且数组的容量大于 64，则将这个链表结构转换为红黑树；然后再插入键值对。注意：**如果remove节点导致红黑树上的节点数量小于6，此时红黑树就会退化为单链表。至于为什么是6，主要是为了防止节点个数在8周围频繁抖动而导致链表与红黑树的频繁装换**。



### 15. 一般用什么作为HashMap的key？用可变对象作为HashMap的key有什么问题？

​		一般用诸如Integer、String这种**不可变对象**作为 HashMap 的 key，而且 String 最为常用。因为不可变对象的不可变特性，所以在对象创建的时候 hashcode 就已经被缓存了，而不需要重新计算。又因为获取对象的时候要用到 `equals()` 和 `hashCode()` 方法，那么键对象正确的重写这两个方法是非常重要的，而这些类已经很规范的重写了 hashCode() 以及 equals() 方法，所以是不二之选。

​		用可变对象作为HashMap的key可能会导致hashcode发生改变，导致 put 进去的值，无法 get 出。如下所示：

```java
public static void main(String[] args) {
    HashMap<ArrayList<Integer>, Integer> map = new HashMap<>();
    ArrayList<Integer> list = new ArrayList<>();
    map.put(list, 1);
    System.out.println(map.get(list));
    // 可变对象发生改变。导致hashcode值发生改变
    list.add(66);
    System.out.println(map.get(list));
}
```



### 20. HashMap为什么线程不安全

- **多线程下扩容死循环**。JDK1.7中的 HashMap 使用头插法插入元素，在多线程的环境下，扩容的时候有可能导致环形链表的出现，形成死循环。因此，JDK1.8使用尾插法插入元素，在扩容时会保持链表元素原本的顺序，不会出现环形链表的问题。
- **多线程的put可能导致元素的丢失**。多线程同时执行 put 操作，如果计算出来的索引位置是相同的，那会造成前一个 key 被后一个 key 覆盖，从而导致元素的丢失。此问题在JDK 1.7和 JDK 1.8 中都存在。
- **put和get并发时，可能导致get为null**。线程1执行put时，因为元素个数超出threshold而导致数组扩容而产生rehash重新计算每个元素的下标，线程2此时执行get，有可能导致这个问题。此问题在JDK 1.7和 JDK 1.8 中都存在。



### 21. HashMap与Hashtable的区别

- Hashtable是线程安全的，HashMap不是线程安全的。其中，Hashtable在关键性的方法前面都加了synchronized关键字，来保证线程的安全性。如果有多个线程会访问到集合，那最好是使用 Hashtable，因为不需要我们自己再去考虑和编写线程安全的代码。值得一提的是，由于Hashtable的执行效率较低，在实际开发中，会用ConcurrentHashMap来代替。
- HashMap的默认初始容量是16，Hashtable的默认初始容量是11；
- HashMap可以使用null作为key，因为HashMap会把null key做特殊处理，即将null key放到数组下标为0的链表|红黑树中进行存储，而Hashtable不允许；
- 它们两个的key的散列算法不同，Hashtable是直接使用key的hashcode对数组长度取模（`int index = (e.hash & 0x7FFFFFFF) % newCapacity;`，其中(e.hash & 0x7FFFFFFF)是为了保证hash值为正数），而HashMap对key的hashcode做了二次散列，从而避免key的分布不均匀问题而影响到查询性能。
- HashMap的迭代器(Iterator)是fail-fast迭代器，而Hashtable的enumerator迭代器不是fail-fast的。所以当有其它线程改变了HashMap的结构（增加或者移除元素），将会抛出ConcurrentModificationException，但迭代器本身的remove()方法移除元素则不会抛出ConcurrentModificationException异常。但这并不是一个一定发生的行为，要看JVM。这条同样也是Enumeration和Iterator的区别。

[(83条消息) HashMap中插入null key的过程分析_hashmap put null_glory-of-me的博客-CSDN博客](https://blog.csdn.net/glory1234work2115/article/details/50825503)



### 22. Iterator和ListIterator有什么区别？

- **遍历元素**。使用Iterator，可以遍历所有集合，如Map，List，Set，但只能在向前方向上遍历集合中的元素；使用ListIterator，只能遍历List实现类的对象，但可以向前和向后遍历集合中的元素。
- **添加元素**。Iterator无法向集合中添加元素，但是ListIteror可以使用`add()`向集合中添加元素。
- **修改元素**。Iterator无法修改集合中的元素，但是ListIterator可以使用`set()`修改集合中的元素。
- **获取元素索引**。Iterator无法获取集合中元素的索引，但是使用ListIterator可以使用`previousIndex()`与`nextIndex()`获取集合中元素的索引。



### 23. HashSet和HashMap的区别

​		HashMap实现的是Map接口，用来存储键值对；而HashSet实现的是Set接口，用来存储对象。HashSet的底层本质上就是一个HashMap，数据存放在HashMap中的key值上，而value值始终使用一个相同的`虚值`来保存。

- HashMap的`put()`方法的返回值为`null`或者`value`
    - 如果map中不存在该key，那么put该key时返回的是null；
    - 如果map中已经存在该key，那么put该key-value时返回的是该key所对应的旧的value。
- HashSet的`add()`方法的返回值为boolean类型
    - 如果插入的数据在集合中存在，则返回false，插入失败；
    - 如果插入的数据在集合中不存在，则返回true，插入成功。



### 24. HashMap在java1.8和java1.7中有什么区别

- **结构不同**

    java1.7是数组+链表结构，java1.8中是数组+链表+红黑树

- **插入方式不同**

    java1.7采用的是头插法，java8采用的是尾插法。

    java1.7先扩容再插入数据，java1.8是先插入数据后扩容

    扩容时java1.7需要rehash，在java1.8中不需要重新计算hash值。



### ConcurrentHashMap的[实现原理](https://www.bilibili.com/video/BV1QS4y1u7gG/)是什么？

​		在数据结构上， JDK1.8 中的ConcurrentHashMap选择了与 HashMap 相同的`数组+链表+红黑树`的结构；在锁的实现上，抛弃了 JDK1.8 以前原有的 [Segment 分段锁](https://www.jianshu.com/p/bd572ccbf73c)，采用`CAS + synchronized`实现更加**低粒度**的锁。将锁的级别控制在了更细粒度的**哈希桶元素级别**，也就是说只需要锁住这个**链表头结点**|**红黑树的根节点**，就不会影响其他的哈希桶元素的读写，大大提高了并发度。



### ConcurrentHashMap的put方法执行原理

大致可以分为以下步骤：

1. 根据 key 计算出 hash值。
2. 如果数组是空的，则进行数组的初始化，默认的初始化容量是16；
3. 根据hash值所计算出的下标位置定位到 Node，拿到首节点 f，并判断首节点 f：

- 如果为 null ，则通过CAS的方式尝试添加。
- 如果为f.hash = MOVED = -1，说明其他线程在扩容，则参与一起扩容。
- 如果都不满足，synchronized 锁住 f 节点，判断是链表还是红黑树，遍历插入。

4. 当在链表长度达到8的时候，数组扩容或者将链表转换为红黑树。



### ConcurrentHashMap的get方法执行原理

1. 根据 key 计算出 hash 值，判断数组是否为空，为空则直接返回null，不为空则继续进行操作；
2. 如果是首节点，就直接返回；
3. 如果是红黑树结构，就从红黑树里面查询；
4. 如果是链表结构，就循环遍历判断。



### volatile关键字的作用？它是怎么实现的？【To be continue~】

​		volatile可以保证变量对所有线程的`可见性`、禁止指令重排序（保证`有序性`），但不保证`原子性`。



### volatile关键字的使用场景

[(83条消息) Java Volatile 的应用场景_volatile使用场景_陈振阳的博客-CSDN博客](https://blog.csdn.net/xichenguan/article/details/119425408)

- 适用场合：高并发场景下，多个线程读，一个线程写的场合
- 使用场景：通常被作为标识完成、中断、状态的标记，值变化应具有原子性
- 充分利用其可见性：即volatile能够保证在读取的那个时刻读到的肯定是最新值
- 重点声明： volatile主要使用的场合是在多线程中可以感知实例变量被变更了，并且可以获得最新的值使用，也就是用多线程读取共享变量时可以获得最新值使用，但不能保证你在使用最新值过程中最新值不发生变化！很可能在使用之后，最新值已经变更。原数据变成过期数据，这时候就会出现数据不一致（非同步）的问题



### 什么是CAS

[(83条消息) 面试题（CAS）_cas面试题_一生酷到底的博客-CSDN博客](https://blog.csdn.net/weixin_43118617/article/details/125601875)

​		CAS（compare and swap），如果多个线程CAS更新同一个变量，只有一个线程可以成功，其它的全部失败，失败的线程可以再次尝试进行更新（自旋CAS）。



### 谈谈快速失败(fail-fast)和安全失败(fail-safe)

1、**快速失败（fail—fast）**：

​		在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出 `ConcurrentModificationException`。（迭代器本身的`remove()`方法移除元素时是不会抛出`ConcurrentModificationException`异常的）

**原理**：迭代器在遍历时会直接访问集合中的内容，并且在遍历过程中使用一个 `modCount` 变量。集合在被遍历期间如果内容发生变化，就会改变 modCount 的值。每当迭代器使用 `hashNext()/next()` 遍历下一个元素之前，都会检测 `modCount` 变量是否等于 `expectedmodCount` 值，相等的话就返回遍历；否则抛出异常，终止遍历。

**注意**：这里异常的抛出条件是检测到 **modCount != expectedmodCount** 这个条件。如果集合发生变化时修改 modCount 值刚好又设置为了 expectedmodCount 值，则异常不会抛出。因此，不能依赖于这个异常是否抛出而进行并发操作的编程，这个异常只建议用于检测**并发修改的 bug**。

**场景：**`java.util包`下的集合类都是`快速失败`的，不能在多线程下发生并发修改（迭代过程中被修改）。



2、**安全失败（fail—safe）**

​		采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是**先复制原有集合内容，然后在拷贝的集合上进行遍历**。

**原理：**由于迭代时是对原集合的拷贝进行遍历，所以在遍历过程中对原集合所作的修改并不能被迭代器检测到，所以不会触发 `ConcurrentModificationException`。

**缺点**：基于拷贝内容的优点虽然避免了 `ConcurrentModificationException`，但同样地，迭代器并不能访问到修改后的内容，即：迭代器遍历的是开始遍历那一刻拿到的集合拷贝，在遍历期间原集合发生的修改迭代器是不知道的。

**场景：**`java.util.concurrent包`下的容器都是安全失败，可以在多线程下并发使用，并发修改。





---

### 总结

| 集合类型  | 默认初始容量 | 扩容规则---是原来的几倍     |
| --------- | ------------ | --------------------------- |
| ArrayList | 10           | 1.5倍                       |
| Vector    | 10           | 2倍                         |
| HashMap   | 16           | 2倍----------负载因子：0.75 |
| Hashtable | 11           | 2倍----------负载因子：0.75 |
| HashSet   | 16           | 2倍                         |





---



[(70条消息) HashMap底层实现原理及面试问题_哪 吒的博客-CSDN博客_hashmap底层实现原理](https://blog.csdn.net/guorui_java/article/details/113827854)

[(72条消息) 【HashMap扩容机制】_我是廖志伟的博客-CSDN博客_hashmap的扩容机制](https://blog.csdn.net/java_wxid/article/details/121599499)

[(72条消息) jdk1.8 HashMap扩容机制变化_学习机器:的博客-CSDN博客_hashmap1.8扩容机制](https://blog.csdn.net/weixin_52373240/article/details/124288884)

[快来看看这份 HashMap 面试小抄 (qq.com)](https://mp.weixin.qq.com/s?__biz=MzkyMTI3Mjc2MQ==&mid=2247485905&idx=1&sn=5416a9bb15128795ffd436e134a390dd&chksm=c1876107f6f0e811532335733aa4b07c3e6f54c9c61dd86d8eb31367da39d298b15e5ae32b35&cur_album_id=2024377861603721219&scene=189#wechat_redirect)



[(82条消息) ThreadLocal详解（附面试题）_threadlocal面试题_ΘLLΘ的博客-CSDN博客](https://blog.csdn.net/qq_45966440/article/details/122330153)



ConcurrentHashMap

ThreadLocal

JVM









