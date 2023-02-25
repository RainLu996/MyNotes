# MQ

## 一、消息队列概念

​		MQ全称为Message Queue，消息队列（MQ）是⼀种`应⽤程序对应⽤程序的通信⽅法`。 应⽤程序通过读写出⼊队列的消息（针对应⽤程序的数据）来通信，⽽⽆需专⽤连接来链接它们。 消息传递指的是程序之间通过在消息中发送数据进⾏通信，⽽不是通过直接调⽤彼此来通信，直接调⽤通常是⽤于诸如`RPC`的技术。



## 二、常⽤的消息队列产品

|                 常用的消息队列产品                 |
| :------------------------------------------------: |
| ![常用的消息队列产品](imgs\常用的消息队列产品.png) |

- RabbitMQ：基于erlang语⾔，稳定可靠、扩展性强、⽀持多协议多客户端、有简单易用的管理界面
- Kafka ⾼吞吐，⾼性能，快速持久化，⽆消息确认，⽆消息遗漏，可能会有有重复消息，依赖zookeeper，成本⾼。
- ActiveMQ：不够灵活轻巧，对队列较多情况⽀持不好。
- RocketMQ：性能好，⾼吞吐，⾼可⽤性，⽀持⼤规模分布式，协议⽀持单⼀



# RabbitMQ

## 一、什么是RabbitMQ

​		RabbitMQ 是使用Erlang语言编写的、实现了`高级消息队列协议（AMQP）`的开源消息中间件。它不负责处理业务，仅仅只是用来**接收并转发消息**。



## 二、AMQP协议

### 2.1 协议定义

​		AMQP(Advanced Message Queueing Protocol)协议是<u>提供统一消息服务</u>的`应用层标准高级消息队列协议`，**是一个进程间传递异步消息的网络协议**。使得遵从该协议的`客户端应用`和`消息中间件服务器`之间的交互成为可能。



### 2.2 核心概念

|               AMQP概念模型图               |
| :----------------------------------------: |
| ![AMQP概念模型图](imgs\AMQP概念模型图.png) |

Broker：代表一个消息中间件应用，负责接收消息生产者的消息，然后将消息发送至消息接受者或者其他的broker。

Virtual Host：这是对broker的虚拟化分组，主要用于对consumer、producer和他们依赖的AMQP相关结构进行隔离。通常是处于安全因素的考虑。

Connection：代表着producer、consumer和broker之间的物理网络（TCP），connection只有在客户端断开连接或者网络问题的时候会断开。

Channel：代表着producer、consumer和broker之间的**逻辑连接**，<u>一个Connection可以包含多个Channel</u>。Channel使得基于同一连接的不同进程之间与broker之间的交互相互隔离，不干扰。而不需要重新建立连接，channel在发生协议错误的时候会被关闭。

Exchange：这是所有被发送的消息首先到达的目的地，Exchange负责根据路由规则将消息路由到不同的目的地。路由规则包括下面几种：direct（point-to-point）、topic（publish-subscribe）和fanout（multicast）。

Queue：这是消息到达的最终目的地，到达queue的消息是已经准备好被消费的消息，一个消息可以被exchange copy发送至多个queue。

Binding：这是queue和exchange之间的虚拟连接，使得消息可以从exchange路由到Queue。routing key可以通过binding和exchange routing规则关联。



### 2.3 AMQP的3层协议

- Module Layer：协议最高层。主要定义了ー些客户端调用的命令，客户端可以用这些命令实现自己的业务逻辑。
- Session Layer：中间层。主要负责客户端命令发送给服务器，再将服务端应答返回给客户端，提供可靠性同步机制和错误处理。
- Transport Layer：最底层。主要传输二进制数据流，提供帧的处理、信道复用、错误检测和数据表示等。

<hr>

### 《相关协议》- 了解

AMQP和其他的协议有什么区别呢，下面简单介绍一下：

- **Java Message Service（JMS）：**与AMQP不同的是，JMS只定义了JAVA编程接口协议，而没有定义消息的具体格式。因此，JMS适用范围较小，只能够用于特定的客户端和broker。而且相比AMQP，JMS没有定义命令交互、消息路由。最后，在JMS中，消息发送者需要知道消息目的 的拓扑。在AMQP中，路由逻辑被封装在了exchange之中，从而保护了消息的发送者。

- **MQ Telemetry Transport（MQTT）**：这是一个极其轻量级的消息队列协议。MQTT只专注于消息发送-消息订阅模型。和AMQP一样，交互性比较强并且适合嵌入式系统，同样它也需要一个broker用于订阅管理和消息路由。RabbitMQ可以和MQTT通信，这是令人兴奋的。

- **ZeroMQ**：提供了消息的一种语义，不需要中心化的broker（但是没有broker提供的持久化和保证送达机制）。是一个可交互的网络编程库，基于多种语言实现。它是一个构建高可用性和高扩展性系统的一种选择。





## 三、RabbitMQ的逻辑结构

- ⽤户（user）
- 虚拟主机（host）-------虚拟主机中包含：__队列、交换机__
- 队列（queue）

虚拟主机相当于一个数据库。

队列相当于数据库中的一张表。

__与数据库操作不同的是：操作每一个host虚拟主机都需要登录账户密码。每一个用户可以绑定多个虚拟主机。未绑定虚拟主机的用户无法访问该虚拟主机。__

|       RabbitMQ的逻辑结构       |
| :----------------------------: |
| ![逻辑结构](imgs\逻辑结构.png) |

|           RabbitMQ的核心组件           |
| :------------------------------------: |
| ![MQ的核心组件](imgs\MQ的核心组件.jpg) |

**<font color="blue">根据RabbitMQ的逻辑结构可知，使用程序连接RabbitMQ需要以下信息：</font>**

- RabbitMQ的所在主机ip
- RabbitMQ的端口号port
- RabbitMQ中的虚拟主机名host-name
- 登录虚拟主机用户的账号密码



## 四、RabbitMQ的入门操作

- 启动RabbitMQ服务

```shell
# 启动RabbitMQ服务
./rabbitmq-server -detached

# 启动RabbitMQ管理界面
./rabbitmq-plugins enable rabbitmq_management

# 可以进行访问了
# RabbitMQ自带可视化界面插件。启动插件设置后，进行访问
http://192.168.153.130:15672/
```



### 4.1 用户管理

![创建用户](imgs\创建用户.png)



### 4.2 虚拟主机管理

![创建虚拟主机](imgs\创建虚拟主机.png)



### 4.3 用户绑定虚拟主机

![进入绑定页面](imgs\进入绑定页面.png)



![用户绑定虚拟主机](imgs\用户绑定虚拟主机.png)



### 4.4 交换机管理

- **fanout：订阅交换机**
- **redirect：路由交换机**

![创建交换机](imgs\创建交换机.png)



### 4.5 队列管理

![创建队列](imgs\创建队列.png)



### 4.6 交换机绑定队列

![交换机绑定队列](imgs\交换机绑定队列.png)





## 五、RabbitMQ⼯作⽅式

### 5.1 图解常用工作方式

> __<font color="red">注意：队列中的某个消息只能被消费一次</font>__

> - RabbitMQ提供了多种消息的通信方式—工作模式     https://www.rabbitmq.com/getstarted.html
> - 消息通信是由两个__角色__完成：消息⽣产者（producer）和 消息消费者（Consumer）

![RabbitMQ⼯作⽅式](imgs\RabbitMQ⼯作⽅式.png)



__而常用的则只有四种：__

- 简单模式

> 一个队列只有一个消费者：⽣产者将消息发送到队列，消费者监听队列，从队列中取出数据。

|                  简单模式                  |
| :----------------------------------------: |
| ![mode1-简单模式](imgs\mode1-简单模式.png) |



- 工作模式

> 多个消费者监听同⼀个队列，__<font color="red">但多个消费者中只有⼀个消费者会成功的消费消息。</font>__
>
> 
>
> ​		工作队列（又称任务队列）的主要作用是避免立即执行__资源密集型任务__，而不得不等待它完成。
>
> ​		将任务封装为消息并将其发送到（工作）队列，在后台运行的处理消息的工作线程将依次执行任务并最终执行完队列中的所有作业（消息）。当有多个工作线程时，这些工作线程将一起处理这些任务。
>
> ​		__<font color="red">由于队列中的消息始终只会被消费一次。所以，队列将会<font color="blue">轮询</font>调用各个工作线程处理消息。</font>__

|                  工作模式                  |
| :----------------------------------------: |
| ![mode2-工作模式](imgs\mode2-工作模式.png) |



- 订阅模式（Fanout Exchange）

> ⼀个交换机绑定多个消息队列，每个消息队列有⼀个消费者监听。

|                  订阅模式                  |
| :----------------------------------------: |
| ![mode3-订阅模式](imgs\mode3-订阅模式.png) |



- 路由模式（Direct Exchange）

> ​		⼀个交换机绑定多个消息队列，每个消息队列都有⾃⼰的key（可以有多个队列使用相同的key），交换机根据指定的key进行消息转发。每个消息队列有⼀个 消费者监听。

|               mode4-路由模式               |
| :----------------------------------------: |
| ![mode4-路由模式](imgs\mode4-路由模式.png) |





### 5.2 基于Maven实现工作方式

- 封装RabbitMQ连接工具类

```java
public class ConnectionUtil {

    private static final String HOST;                  // RabbitMQ所在主机ip
    private static final Integer PORT;                 // RabbitMQ所在主机端口号
    private static final String VIRTUAL_HOST_NAME;     // RabbitMQ中的虚拟主机名（相当于数据库名）
    private static final String USERNAME;              // 对应可以操作虚拟主机的用户名
    private static final String PASSWORD;              // 对应可以操作虚拟主机的密码

    private ConnectionUtil() {
    }

    public static final ResourceBundle bundle = ResourceBundle.getBundle("rabbitMQ");

    static {
        HOST = bundle.getString("host");
        PORT = Integer.parseInt(bundle.getString("port"));
        VIRTUAL_HOST_NAME = bundle.getString("virtualHostName");
        USERNAME = bundle.getString("username");
        PASSWORD = bundle.getString("password");
    }

    public static Connection getConnection() throws IOException, TimeoutException {
        //1.创建连接⼯⼚
        ConnectionFactory factory = new ConnectionFactory();

        //2.在⼯⼚对象中设置MQ的连接信息(ip, port, virtualhost, username, password)
        factory.setHost(HOST);
        factory.setPort(PORT);
        factory.setVirtualHost(VIRTUAL_HOST_NAME);
        factory.setUsername(USERNAME);
        factory.setPassword(PASSWORD);

        //3.通过⼯⼚对象获取与MQ的链接
        return factory.newConnection();
    }

}
```

```properties
host=116.62.157.189
port=5672
virtualHostName=host1
username=rainlu
password=RainLu666
```



- 消息生产者

```java
public class sendMsg {

    // 创建的队列名
    public static final String QUEUE_NAME = "hello_world";

    public static void main(String[] args) {
        senMsg("HelloWorld");
    }
    
    public static void senMsg(String simpleMsg) {

        try {
            // 获取连接
            Connection connection = ConnectionUtil.getConnection();

            // 获取信道
            //channel 实现了自动 close 接口 自动关闭 不需要显示关闭
            Channel channel = connection.createChannel();

            /**
             * 生成一个队列【queueDeclare】：参数解析
             *      1.想生成的队列名称
             *      2.队列是否持久化，默认队列存储在内存中。【RabbitMQ服务重启后，此队列就会消失。】
             *      3.该队列是否只供一个消费者进行消费，是否进行共享。true：可以多个消费者消费
             *      4.是否自动删除 最后一个消费者断开连接以后 该队列是否自动删除 true 自动删除
             *      5.其他参数
             */
            // 使用Java程序创建一个队列
            boolean isDurable = false;   // 指定队列不会持久化
            channel.queueDeclare(QUEUE_NAME, isDurable, false, false, null);

            /**
             * 发送一个消息【basicPublish】：参数解析
             *     1.发送到哪个交换机。（没有交换机就不用指定交换机，直接写个空串）
             *     2.路由的 key 是哪个。（没有交换机->没有key 的时候，就是指定队列名）
             *     3.其他的参数信息。
             *      · MessageProperties.MINIMAL_PERSISTENT_BASIC：设置消息持久化。
             *     4.发送消息的消息体。（消息需要使用字节表示！）
             */
            channel.basicPublish("", QUEUE_NAME, null, simpleMsg.getBytes());

            System.out.println("success---------------");

        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```



- 消息消费者

```java
public class receiveMsg {

    public static final String QUEUE_NAME = "hello_world";

    public static void main(String[] args) {
        consumeMsg();
    }

    public static void consumeMsg(){


        try {
            Connection connection = ConnectionUtil.getConnection();

            Channel channel = connection.createChannel();

            // 声明正常处理消息的回调接口（消息接收成功之后，将会调用此回调方法）
            DeliverCallback deliver = (consumerTag, message) -> {
                System.out.println("consumerTag: " + consumerTag);
                System.out.println(new String(message.getBody()));

                /**
                 * @param deliveryTag:long
                 * @param multiple:boolean
                 */
                channel.basicAck();
                /**
                 * @param deliveryTag:long
                 * @param multiple:boolean
                 * @param requeue:boolean
                 */
                channel.basicNack();
                /**
                 * @param deliveryTag:long
                 * @param requeue:boolean
                 */
                channel.basicReject();
            };

            // 声明取消消息接收的回调接口（消息接收成功之后，此方法无用）
            CancelCallback cancel = consumerTag -> {
                System.out.println(consumerTag + ": 消费中断~");
            };

            /**
             * 消费者消费消息【basicConsume】：参数解析
             *     1.消费哪个队列
             *     2.消费成功之后是否要自动应答 true 代表自动应答 false 手动应答
             *     3.消费者未成功消费的回调。
             *     4.消费者取消消费操作的回调。（如果消息已经正常被接收，此方法将没有使用意义）
             */
            channel.basicConsume(QUEUE_NAME, true, deliver, cancel);

            System.out.println("waiting……");
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }
    }

}

```



### 5.3 RabbitMQ中各个名词介绍

- RabbitMQ工作原理图解

![RabbitMQ工作原理](imgs\RabbitMQ工作原理.png)

- 名词解释

> Broker：接收和分发消息的应用，RabbitMQ Server 就是 Message Broker。
>
> Virtual host：出于多用户和安全因素设计的，把 AMQP 的基本组件划分到一个虚拟的分组中，类似于网络中的 namespace 概念。当多个不同的用户使用同一个 RabbitMQ server 提供的服务时，可以划分出多个 vhost，每个用户在自己的 vhost 创建 exchange／queue 。
>
> Connection：publisher／consumer 和 broker 之间的 TCP 连接。
>
> Channel：如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 TCP Connection 的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个 thread 创建单独的 channel 进行通讯，AMQP method 包含了 channel id 帮助客户端和 message broker 识别 channel，所以 channel 之间是完全隔离的。Channel 作为轻量级的Connection 极大减少了操作系统建立 TCP connection 的开销。
>
> Exchange：message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，分发消息到 queue 中去。常用的类型有：direct (point-to-point), topic (publish-subscribe) and fanout (multicast)。
>
> Queue：消息最终被送到这里等待 consumer 取走。
>
> Binding：exchange 和 queue 之间的虚拟连接，binding 中可以包含 routing key，Binding 信息被保
> 存到 exchange 中的查询表中，用于 message 的分发依据。



### 5.4 不公平分发

> ​		原理就是：当预取值设置为1时，当前queue连接consumer的`channel`中所能存在的未被ack的消息只能有一个，超过一个之后，当前consumer不再接收消息。此消息爱上哪就上哪！只有等当前channel中的消息被ack，才会继续接收下一个消息进行处理。<u>这样，就能做到，能者多劳。</u>

​		这种方式用来解决使用`轮询方式`所存在的一些问题：

​		比方说有两个消费者在处理任务，其中有个消费者 1 处理任务的速度非常快，而另外一个消费者 2 处理速度却很慢。**这个时候如果我们还是采用轮训分发的方式，就会导致处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活。**这种分配方式在这种情况下其实就不太好，但是 RabbitMQ 并不知道这种情况，所以它依然很公平的（默认值）进行分发。

<hr>
​	在**<font color="red">消费者方</font>**设置参数来实现不公平分发：`channel.basicQos(1);`

> ​		意思就是如果这个任务我还没有处理完或者我还没有应答你，你先别分配给我，我目前只能处理一个任务，然后 RabbitMQ 就会把该任务分配给没有那么忙的那个空闲消费者。
>
> ​		**当然如果所有的消费者都没有完成手上任务，队列还在不停的添加新任务，有可能就会遇到队列被撑满的情况，这个时候就只能添加新的 worker消费者 或者使用其它处理任务的策略。**

<hr>


​	**设置不公平分发之后，查看RabbitMQ管理界面，发现`channels`中当前消费者信道的`Prefetch`值为1。**

​	此时，再进行消息发送，会发现：**<font color="blue">消息接收方处理速度慢的接收到的消息少；而处理速度快的那一方接受到的消息多。</font>**



![不公平分发](imgs\不公平分发.png)



### 5.5 预取值分发

​		本身消息的发送就是异步的，所以在任何时候，`channel` （<u><font color="green">也可以理解为：存放未确认消息的缓冲区</font></u>）中肯定不止只有一个消息。

​		另外，来自消费者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，有时候会希望开发人员能限制此缓冲区的大小，以避免缓冲区里面无限制的存放未确认的消息。这个时候就可以通过使用 `channel.basicQos(消息预取值);` 方法设置**<font color="red">预取计数值</font>**来解决这个问题。

​		**<font color="green">该值定义在当前通道上允许保留的未确认消息的最大数量。</font>**一旦当前通道中消息的数量达到配置的数量， RabbitMQ 将停止在通道中传递更多消息，除非至少有一个未处理的消息被确认。==注意：不是根据消费者消费了多少消息来计数，而是根据当前通道channel中保留的未确认的消息数来计数。==

​		例如，假设在通道上有 未确认的消息`msg-5、msg-6、msg-7，msg-8`，并且通道的<u>预取值</u>设置为 4，此时 RabbitMQ 将不会往该通道上再传递任何消息，**除非至少有一个未应答的消息被 ack。**比方说 tag=6 这个消息刚刚被确认 ACK，RabbitMQ 将会感知 这个情况到并再发送一条消息。

​		**消息应答和 `QoS 预取值`对用户吞吐量有重大影响。**通常，**<font  color="red">增加预取值将提高向消费者传递消息的速度。</font>**虽然自动应答传输消息速率是最佳的，但是，在这种情况下**已传递但尚未处理的消息的数量也会增加**，从而**增加了消费者的 RAM 消耗(随机存取存储器)**。

​		<u>应该小心使用具有无限预处理的自动确认模式或手动确认模式！</u>如果消费者接收了大量的消息但是没有ack的话，会导致消费者连接节点的内存消耗变大。所以找到`合适的预取值`是一个反复试验的过程，不同的负载该值取值也不同。`100 到 300 范 围内的值通常可提供最佳的吞吐量`，并且不会给消费者带来太大的风险与负担。预取值为 1 是最保守的，当然这将使吞吐量变得很低，特别是消费者连接延迟很严重、连接等待时间较长的环境下。所以，对于大多数应用来说，预取值需要结合当前机器的性能或其它方面做综合考虑。



## 六、RabbitMQ持久化

> ​		为了保障当 RabbitMQ 服务停掉以后消息生产者发送过来的消息不丢失，我们需要使用RabbitMQ的消息持久化策略。默认情况下 RabbitMQ 退出或由于某种原因崩溃时，没有设置**持久化保存**的队列和消息将会从内存中消失。
>
> ​	==**<font color="red">要想确保消息不会丢失需要做两件事：将队列和消息都标记为持久化。持久化交换机只能确保交换机不会被销毁，而与消息可靠性无关。</font>**==



### 6.1 交换机持久化

> ​		交换机的持久化其实就是相当于将交换机的属性在服务器内部保存。当MQ的服务器发生意外或关闭之后，重启RabbitMQ时不需要再手动去建立交换机，交换机会一直存在。
>
> ​		**<font color="red">值得注意的是：持久化交换机与消息是否会丢失无关，只是不能继续将消息发送到这个交换机中了</font>**。

```java
channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT, durable);
```



### 6.2 队列持久化

> ​		注意：如果之前声明的队列不是持久化的，就需要把原先的队列先删除，再重新创建一个持久化的队列，不然就会出现错误。
>
> ![重新创建持久化队列](imgs\重新创建持久化队列.png)
>
> 
>
> ​		在RabbitMQ的管理界面查看持久化队列，发现**Features显示为D**。这代表该队列为持久化队列。这个时候即使重启 rabbitmq 队列也依然存在。
>
> ![在RabbitMQ的管理界面查看持久化队列](imgs\在RabbitMQ的管理界面查看持久化队列.png)

- 消息生产者


```java
// 在声明队列时，设置队列的持久化参数
boolean isDurable = ture;   // 指定队列会被持久化
channel.queueDeclare(QUEUE_NAME, isDurable, false, false, null);
```



#### 补充：临时队列

​		每当我们连接到 RabbitMQ 时，我们都需要一个全新的空队列，为此我们可以创建一个具有随机名称的队列，或者能让服务器为我们选择一个随机队列名称那就更好了。其次，一旦我们断开了消费者的连接，队列将被自动删除。

​		创建方式：`String queueName = channel.queueDeclare().getQueue();`





### 6.3 消息持久化

> ​		==将消息标记为持久化并不能完全保证不会丢失消息，尽管它告诉 RabbitMQ 要将消息保存到磁盘，还仍存在当消息刚准备存储到磁盘的时候却还没有全部存储完的情况。==即：**消息存在一个持久化的间隔点。**在这个间隔点内，消息还并没有真正的被完全写入磁盘。
>

- 消息生产者

```java
// 更改消息生产者的属性：添加参数-->MessageProperties.PERSISTENT_TEXT_PLAIN
channel.basicPublish("", QUEUE_NAME, MessageProperties.PERSISTENT_TEXT_PLAIN, simpleMsg.getBytes());
```





## 七、交换机

### 7.1 Exchange 概念

​		RabbitMQ 消息传递模型的核心思想是： **<font color="red">生产者生产的消息从不会直接发送到队列。</font>**实际上，生产者甚至都不知道这些消息传递传递到了哪些队列中。

​		相反，**生产者只能将消息发送到交换机(exchange)**，交换机工作的内容非常简单，**<font color="green">一方面它接收来自生产者的消息，另一方面将它们推入队列。</font>**交换机必须确切知道如何处理收到的消息：是应该把这些消息推送至特定的队列；还是说要把这些消息推送至其它所绑定的所有队列中；还是说应该丢弃它们。这就需要由交换机的***类型***来决定。

![交换机概念](imgs\交换机概念.png)



### 7.2 无名Exchange

​		在以上对RabbitMQ的基本操作中，我们虽然无视了 Exchange的概念，但仍然能够将消息发送到队列。能实现的原因是因为我们使用的是**默认交换**，我们通过**<font color="blue">空字符串(“”)</font>**进行标识。 

`channel.basicPublish("", queueName, null, message.getBytes());`		

​		第一个参数是交换机的名称。空字符串表示**默认或无名称交换机**。消息能路由发送到队列中其实 是由 `routingKey(bindingkey)`绑定 key 指定的，前提是如果它存在的话。



### 7.3 绑定(bindings)

​		binding 其实是 exchange 和 queue 之间的桥梁，它告诉我们 exchange 和哪个队列进行了绑定关系。比如说下面这张图告诉我们的就是 X 与 Q1 和 Q2 进行了绑定。

​		**<font color="red">队列只对它绑定的交换机中的消息感兴趣。</font>**

​		绑定用参数`routingKey`来表示，也可称该参数为`bindingKey`。

​		创建绑定关系我们用代码：`channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "routingKey");`绑定之后的意义由其交换类型决定。

![绑定示意图](imgs\绑定示意图.png)



### 7.4 Exchange的常用类型

#### a. Fanout（订阅模式）

> ​	将接收到的所有消息广播到它所绑定的所有队列中。系统中默认存在一些 exchange 类型的队列。

**案例架构图**

![Fanout案例结构](imgs\Fanout案例结构.png)



- 消费者（可以多复制几份进行测试，只需要保证连接对队列即可）

```java
private static final String EXCHANGE_NAME = "logs";
private static final String QUEUE_NAME = UUIDUtil.getUUID();

public static void main(String[] args) throws IOException, TimeoutException {

    // 获取信道连接
    Channel channel = ConnectionUtil.getChannel();


    // 生成一个临时的队列，队列的名称是随机的，当消费者断开和该队列的连接时，队列自动删除
    channel.queueDeclare(QUEUE_NAME, false, false, false, null);

    // 把创建的临时队列绑定 exchange（Fanout方式）。
    // 其中，routingkey(也称之为 binding key)，为空字符串。代表该交换机会将消息转发给所有与其绑定的队列。
    channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "");

    // 接收消息
    System.out.println("消费者1-等待接收消息,把接收到的消息打印在屏幕.....");
    DeliverCallback deliverCallback = (consumerTag, delivery) -> {
        String message = new String(delivery.getBody(), StandardCharsets.UTF_8);
        System.out.println("控制台打印接收到的消息" + message);
    };
    channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> {});
}
```



- 消息提供者

```java
private static final String EXCHANGE_NAME = "logs";

public static void main(String[] argv) throws Exception {
    try (Channel channel = ConnectionUtil.getChannel()) {
        /**
             *              声明一个 exchange
             * @param exchange:String 交换机的名称
             * @param type:String 交换机的类型
             */
        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入信息：");
        while (sc.hasNext()) {
            String message = sc.next();
            channel.basicPublish(EXCHANGE_NAME, "", null, message.getBytes(StandardCharsets.UTF_8));
            System.out.println("生产者发出消息" + message);
        }
    }
}
```





#### b. Direct（路由模式）

> ​		Fanout 这种交换类型并不能给我们带来很大的灵活性，因为它只能进行无意识的**广播**。在这里我们将使用 Direct 这种交换机类型来进行消息分发。
>
> ​		Direct （路由模式）的工作方式是：**消息只被分发到它绑定的 routingKey 队列中去。**



##### **1. routingKey 绑定类型**

- routingKey 不重复绑定

![routingKey 不重复](imgs\routingKey 不重复绑定.png)

> ​		在上面这张图中，我们可以看到 X 绑定了两个队列，绑定类型是 Direct。队列 Q1 绑定键为 orange， 队列 Q2 绑定键有两个：一个绑定键为 black，另一个绑定键为 green。在这种绑定情况下，生产者发布消息到 exchange 上，绑定键为 orange 的消息会被发布到队列 Q1。绑定键为 black或者green 和的消息会被发布到队列 Q2。而其它的绑定建类型的消息将被丢弃。



- routingKey 重复绑定（多重绑定）

![routingKey重复绑定](imgs\routingKey重复绑定.png)

> ​		如上图所示，如果 exchange 的类型是 Direct，并且它绑定的多个队列的 key 都相同。
>
> ​		在这种情况下，虽然交换机类型是 Direct ，但是它的功能表现却与 Fanout 十分类似。





##### **2. 案例演示**

**案例架构图**

<img src="imgs\Direct案例架构图.png" alt="Direct案例架构图" style="zoom:67%;" />

<img src="imgs\Direct案例架构图2.png" alt="Direct案例架构图2" style="zoom: 67%;" />

<hr>

**代码实现与Fanout差不多，只是需要指定routingKey 而已。**


- 消费者

```java
try (Channel channel = ConnectionUtil.getChannel()) {
    // 声明队列
    channel.queueDeclare(QUEUE_NAME, false, false, false, null);
    // 将声明的队列绑定交换机，并指定routingKey。
    // 表示消息发送方的routingKey只要与此处指定的一致，就会被此队列接收。
    channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "error");
    
    // 若要绑定多个，就再写几个queueBind……
    // …… 

    System.out.println("等待接收消息.....");
    // 正常接收消息回调接口
    DeliverCallback deliverCallback = (consumerTag, delivery) -> {
        String message = new String(delivery.getBody(), "UTF-8");
        System.out.println(" 接收绑定键 :" + delivery.getEnvelope().getRoutingKey() + ", 消息:" + message);
    };
    // 连接队列---处理消息
    channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> {
    });
} ……
```



- 消息提供者

```java
try (Channel channel = ConnectionUtil.getChannel()) {
    // 声明一个“路由交换机”
    channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);

    //创建多个 bindingKey
    Map<String, String> bindingKeyMap = new HashMap<>();
    bindingKeyMap.put("info", "普通 info 信息");
    bindingKeyMap.put("warning", "警告 warning 信息");
    bindingKeyMap.put("error", "错误 error 信息");
    //debug 没有消费这接收这个消息，会被丢失
    bindingKeyMap.put("debug", "调试 debug 信息");

    for (Map.Entry<String, String> bindingKeyEntry : bindingKeyMap.entrySet()) {
        String bindingKey = bindingKeyEntry.getKey();
        String message = bindingKeyEntry.getValue();
        channel.basicPublish(EXCHANGE_NAME, bindingKey, null, message.getBytes("UTF-8"));
        System.out.println("生产者发出消息:" + message);
    }
} ……
```





#### c. Topic（主题模式）

1. Direct交换机存在的问题

 ​		尽管使用 direct 交换机改进了我们的系统，但是它仍然存在局限性。

 ​		比方说我们想接收的日志类型有 info.base 和 info.advantage，而某个队列只想接收 info.base 的消息，那么 direct 就办不到了**（因为info中的base与advantage消息都会被指定routingKey为info的队列接收）**。这个时候就只能使用 topic 类型了。



​	

2. 使用Topic的要求

> ​		发送到类型是 topic 交换机的消息的 routing_key 不能随意写，必须满足一定的要求，**它必须是一个单 词列表，以点号分隔开。**这些单词可以是任意单词，比如说："stock.usd.nyse"，"nyse.vmw",  "quick.orange.rabbit"这种类型的。**当然这个单词列表最多不能超过 255 个字节。** 
>
> ​		在这个规则列表中，其中有两个替换符是大家需要注意的 
>
> - **<font color="green">*(星号)可以代替一个单词</font>** 
> - **<font color="green">#(井号)可以替代零个或多个单词</font>**
>
> 
>
> **当队列绑定关系是下列这种情况时需要引起注意**
>
> - **<font color="red">当一个队列绑定键是#，那么这个队列将接收所有的数据，神似 fanout </font>** 
>
> - **<font color="red">如果队列绑定键当中没有#和*出现，那么该队列绑定类型神似 direct </font>**



3. 案例架构

![Topic模式案例架构](imgs\Topic模式案例架构.png)

<img src="imgs\RabbitMQ管理界面Topic模式绑定交换机.png" alt="RabbitMQ管理界面Topic模式绑定交换机" style="zoom:67%;" />

```
quick.orange.rabbit            被队列 Q1Q2 接收到
lazy.orange.elephant           被队列 Q1Q2 接收到
quick.orange.fox               被队列 Q1 接收到
lazy.brown.fox                 被队列 Q2 接收到
lazy.pink.rabbit 			   虽然满足两个绑定但只被队列 Q2 接收一次
quick.brown.fox 			   不匹配任何绑定不会被任何队列接收到会被丢弃
quick.orange.male.rabbit 	   四个单词，但不匹配任何绑定将会被丢弃
lazy.orange.male.rabbit 	   四个单词，但匹配 Q2
```



4. 代码实操

- 消息提供者

```java
try (Channel channel = ConnectionUtil.getChannel()) {
    channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
    /**
             * Q1-->绑定的是
             *      中间带 orange 带 3 个单词的字符串(*.orange.*)
             *
             * Q2-->绑定的是
             *      最后一个单词是 rabbit 的 3 个单词(*.*.rabbit)
             *      第一个单词是 lazy 的多个单词(lazy.#)
             */
    Map<String, String> bindingKeyMap = new HashMap<>();
    bindingKeyMap.put("quick.orange.rabbit", "被队列 Q1Q2 接收到");
    bindingKeyMap.put("lazy.orange.elephant", "被队列 Q1Q2 接收到");
    bindingKeyMap.put("quick.orange.fox", "被队列 Q1 接收到");
    bindingKeyMap.put("lazy.brown.fox", "被队列 Q2 接收到");
    bindingKeyMap.put("lazy.pink.rabbit", "虽然满足两个绑定但只被队列 Q2 接收一次");
    bindingKeyMap.put("quick.brown.fox", "不匹配任何绑定不会被任何队列接收到会被丢弃");
    bindingKeyMap.put("quick.orange.male.rabbit", "是四个单词不匹配任何绑定会被丢弃");
    bindingKeyMap.put("lazy.orange.male.rabbit", "是四个单词但匹配 Q2");

    for (Map.Entry<String, String> bindingKeyEntry : bindingKeyMap.entrySet()) {
        String bindingKey = bindingKeyEntry.getKey();
        String message = bindingKeyEntry.getValue();
        channel.basicPublish(EXCHANGE_NAME, bindingKey, null, message.getBytes("UTF-8"));
        System.out.println("生产者发出消息" + message);
    }
}
```



- 消费者

```java
try (Channel channel = ConnectionUtil.getChannel()) {
    channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
    channel.queueDeclare(QUEUE_NAME, false, false, false, null);
    channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "*.*.rabbit");
    channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "lazy.#");
    System.out.println("等待接收消息.....");
    DeliverCallback deliverCallback = (consumerTag, delivery) -> {
        String message = new String(delivery.getBody(), "UTF-8");
        System.out.println(" 接收队列 :" + QUEUE_NAME + " 绑定键:" + delivery.getEnvelope().getRoutingKey() + ", 消息:" + message);
    };
    channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> {
    });
} 
```





## 八、死信队列

### 8.1 什么是死信

> ​		**<font color="red">死信，就是无法被消费的消息。</font>**一般来说，producer 将消息投递到 broker 或者直接到 queue 中，consumer 从 queue 取出消息进行消费。但某些时候由于特定的原因导致 queue 中的某些消息无法被消费，**像这样的消息如果没有后续的处理，就变成了死信。**
>
> ​		**<font color="red">专门用来存放死信的队列就称为死信队列。</font>** 



### 8.2 死信的来源

- 消息 TTL 过期
- 队列达到最大长度（队列满了，无法再添加数据到 mq 中）
- 消息被拒绝(basic.reject 或 basic.nack)并且 requeue=false



### 8.3 演示

1. 代码架构图

![死信实战架构图](imgs\死信实战架构图.png)



2. 消息的TTL过期

- 生产者

```java
try (Channel channel = ConnectionUtil.getChannel()) {
    // 声明转发至普通队列的交换机
    channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);

    /* 设置消息的 TTL 时间 */
    AMQP.BasicProperties properties = new
        AMQP.BasicProperties().builder().expiration("10000").build();

    //该信息是用作演示队列个数限制
    for (int i = 1; i < 11; i++) {
        Thread.sleep(1000);  // 让相邻消息的发送有时间间隔，便于观察死信转移至死信队列
        String message = "info" + i;
        channel.basicPublish(NORMAL_EXCHANGE, "zhangsan", properties,
                             message.getBytes());
        System.out.println("生产者发送消息:" + message);
    }
}
```



- 消费者1**<font color="green">（启动之后关闭该消费者 模拟其接收不到消息）</font>**

```java
public static void main(String[] argv) throws Exception {
    Channel channel = ConnectionUtil.getChannel();
    //声明死信和普通交换机 类型为 direct
    channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
    channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
    //声明死信队列
    channel.queueDeclare(DEAD_QUEUE, false, false, false, null);
    //死信队列绑定死信交换机与 routingkey
    channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "lisi");
    //正常队列绑定死信队列信息
    Map<String, Object> params = new HashMap<>();
    //可以在此处统一设置消息的过期时间，但是这样做不灵活。一般是在消息发送时，设置TTL。
    //正常队列设置死信交换机 参数 key 是固定值
    params.put("x-dead-letter-exchange", DEAD_EXCHANGE);
    //正常队列设置死信 routing-key 参数 key 是固定值
    params.put("x-dead-letter-routing-key", "lisi");

    channel.queueDeclare(NORMAL_QUEUE, false, false, false, params);
    channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "zhangsan");
    System.out.println("等待接收消息.....");
    DeliverCallback deliverCallback = (consumerTag, delivery) -> {
        String message = new String(delivery.getBody(), "UTF-8");
        System.out.println("Consumer01 接收到消息" + message);
    };
    channel.basicConsume(NORMAL_QUEUE, true, deliverCallback, consumerTag -> {
    });
}
```



- 消费者2

```java
public static void main(String[] argv) throws Exception {
    Channel channel = ConnectionUtil.getChannel();
    channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
    channel.queueDeclare(DEAD_QUEUE, false, false, false, null);
    channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "lisi");

    System.out.println("等待接收死信队列消息.....");
    DeliverCallback deliverCallback = (consumerTag, delivery) -> {
        String message = new String(delivery.getBody(), "UTF-8");
        System.out.println("Consumer02 接收死信队列的消息" + message);
    };
    channel.basicConsume(DEAD_QUEUE, true, deliverCallback, consumerTag -> {
    });
}
```



3. 队列达到最大长度

- 生产者（去掉TTL属性）

```java
try (Channel channel = ConnectionUtil.getChannel()) {
    // 声明转发至普通队列的交换机
    channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);

    // 删除为消息设定的TTL……
    
    //该信息是用作演示队列个数限制
    for (int i = 1; i < 11; i++) {
        Thread.sleep(1000);  // 让相邻消息的发送有时间间隔，便于观察死信转移至死信队列
        String message = "info" + i;
        channel.basicPublish(NORMAL_EXCHANGE, "zhangsan", properties,
                             message.getBytes());
        System.out.println("生产者发送消息:" + message);
    }
}
```



- 消费者1（添加参数：队列元素的最大长度）**<font color="green">（启动之后关闭该消费者 模拟其接收不到消息）</font>**

```java
public static void main(String[] argv) throws Exception {
    Channel channel = ConnectionUtil.getChannel();
    //声明死信和普通交换机 类型为 direct
    channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
    channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
    //声明死信队列
    channel.queueDeclare(DEAD_QUEUE, false, false, false, null);
    //死信队列绑定死信交换机与 routingkey
    channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "lisi");
    //正常队列绑定死信队列信息
    Map<String, Object> params = new HashMap<>();
    //可以在此处统一设置消息的过期时间，但是这样做不灵活。一般是在消息发送时，设置TTL。
    //正常队列设置死信交换机 参数 key 是固定值。指定队列中产生的死信转发到哪个死信交换机。
    params.put("x-dead-letter-exchange", DEAD_EXCHANGE);
    //正常队列设置死信 routing-key 参数 key 是固定值。根据routingkey来转发消息。
    params.put("x-dead-letter-routing-key", "lisi");
    // 修改-------------
    //正常队列设置队列元素最大长度限制
    params.put("x-max-length", 6);

    channel.queueDeclare(NORMAL_QUEUE, false, false, false, params);
    channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "zhangsan");
    System.out.println("等待接收消息.....");
    DeliverCallback deliverCallback = (consumerTag, delivery) -> {
        String message = new String(delivery.getBody(), "UTF-8");
        System.out.println("Consumer01 接收到消息" + message);
    };
    channel.basicConsume(NORMAL_QUEUE, true, deliverCallback, consumerTag -> {
    });
}
```



- 消费者2（不变，依然用来接收死信队列中的消息）

……





4. 消息被拒

- 消息生产者（代码同【队列达到最大长度】中的生产者一致，都没有为消息设置TTL）

……



- 消费者1

```java
public static void main(String[] argv) throws Exception {
    Channel channel = ConnectionUtil.getChannel();
    //声明死信和普通交换机 类型为 direct
    channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
    channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
    //声明死信队列
    channel.queueDeclare(DEAD_QUEUE, false, false, false, null);
    //死信队列绑定死信交换机与 routingkey
    channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "lisi");
    //正常队列绑定死信队列信息
    Map<String, Object> params = new HashMap<>();
    //可以在此处统一设置消息的过期时间，但是这样做不灵活。一般是在消息发送时，设置TTL。
    //正常队列设置死信交换机 参数 key 是固定值。指定队列中产生的死信转发到哪个死信交换机。
    params.put("x-dead-letter-exchange", DEAD_EXCHANGE);
    //正常队列设置死信 routing-key 参数 key 是固定值。根据routingkey来转发消息。
    params.put("x-dead-letter-routing-key", "lisi");

    channel.queueDeclare(NORMAL_QUEUE, false, false, false, params);
    channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "zhangsan");
    
    System.out.println("等待接收消息.....");
    // 修改-------------
    // 拒绝接收消息：info5
    DeliverCallback deliverCallback = (consumerTag, delivery) -> {
        String message = new String(delivery.getBody(), "UTF-8");
        if(message.equals("info5")){
            System.out.println("Consumer01 接收到消息" + message + "并拒绝签收该消息");
            //requeue 设置为 false 代表拒绝重新入队 该队列如果配置了死信交换机将发送到死信队列中
            channel.basicReject(delivery.getEnvelope().getDeliveryTag(), false);
        }else {
            System.out.println("Consumer01 接收到消息"+message);
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
        }
    };
    boolean autoAck = false;
    channel.basicConsume(NORMAL_QUEUE, autoAck, deliverCallback, consumerTag -> {
    });
}
```



- 消费者2（不变，依然用来接收死信队列中的消息）

……





## 九、延时队列

### 9.1 什么是延时队列

> ​		延时队列就是用来存放需要**在指定时间被处理**的消息的队列。
>
> ​		若需要在某个事件发生之后或者之前的指定时间点完成某一项任务，就可以使用延时队列来实现。这样，就替代了原先轮询数据的操作。提升了性能，降低了数据库压力。

​	

### 9.2 RabbitMQ 中的 TTL

> ​		TTL 是 RabbitMQ 中一个消息或者队列的属性，用来设置一条消息或者该队列中的所有消息的最大存活时间，单位是毫秒。
>
> ​		换句话说，如果一条消息设置了 TTL 属性或者进入了设置 TTL 属性的队列，那么这条消息如果在 TTL 设置的时间内没有被消费，则会成为"死信"。**如果同时配置了队列的 TTL 和消息的 TTL，那么<font color="blue">较小</font>的那个值将会被使用。**
>
> > **<font color="green">误区提示：设置队列的TTL不是指队列的生存时间，而是队列中消息的生存时间。</font>**



- 消息设置TTL：使用**Expiration**参数

![消息设置TTL](imgs\消息设置TTL.png)



- 队列设置TTL：使用**x-message-ttl**参数

![队列设置TTL](imgs\队列设置TTL.png)



#### ==消息设置TTL与队列设置TTL的区别==

> **<font color="green">两者的区别在于判断消息是否过期的时间不同。</font>**
>
> - **消息属性设置TTL：** 因为通过消息属性设置TTL，发送到队列的消息是各不相同的，如果你想要判断消息是否过期，那么就要扫描整个队列，所以索性等到消息即将要发送给消费者时（队列头），判断消息是否过期，如果消息过期则抛弃或者进入死信队列。
>
> - **队列设置消息TTL：** 因为通过队列设置消息TTL，整个队列中的过期时间是一样的（不另外通过消息属性设置TTL），那么过期的消息势必出现在队列头一整段，这个时候从队列头扫描（定时扫描）到没有过期的消息前，将这一段的消息全部抛弃或者进入死信队列。





### 9.3 基于SpringBoot使用RabbitMQ

> ​		SpringBoot应用可以完成自动配置及依赖注入——可以通过SpringBoot直接提供与MQ的连接对象。

1. 依赖引入

![RabbitMQ-SpringBoot依赖](imgs\RabbitMQ-SpringBoot依赖.png)



2. 配置文件

```yml
server:
  port: 8080
spring:
  application:
    name: rabbitmq-SB
  rabbitmq:
    host: 116.62.157.189
    port: 5672
    virtual-host: host1
    username: rainlu
    password: RainLu666
```



### 9.4 RabbitMQ原生实现延时队列

> ​		**RabbitMQ本身是不支持延时队列的，但是可以借助死信队列来实现。**

*==**【在队列处实现消息延迟】**==*

#### a. 方案一

​		==给队列设置TTL==

|            RabbitMQ-SpringBoot实现延时队列未优化             |
| :----------------------------------------------------------: |
| ![RabbitMQ-SpringBoot实现演示队列未优化](imgs\RabbitMQ-SpringBoot实现演示队列未优化.png) |

> ​		**这种方案，当每增加一个新的延时队列需求，就要新增一个队列，因为这里只有 10S 和 40S 两个时间选项。**
>
> ​		如果需要一个小时后处理，那么就需要增加 TTL 为一个小时的队列；如果是预定会议室然 后提前通知这样的场景，岂不是要增加无数个队列才能满足需求？显然，这种方案是不可取的！



#### b. 方案二

​		==给消息设置TTL==

|            RabbitMQ-SpringBoot实现延时队列优化后             |
| :----------------------------------------------------------: |
| ![RabbitMQ-SpringBoot实现延时队列优化后](imgs\RabbitMQ-SpringBoot实现延时队列优化后.png) |

> **解决方案：**
>
> ​		**相比方案一，新增一个队列 QC，该队列不设置 TTL 时间。只让消息提供者提供的<font color="blue">消息</font>进行TTL设置。**
>
> ​		
>
> **新的问题：**
>
> ​		使用这种方法，看起来似乎没什么问题，但是在（[消息设置TTL的特征](#消息设置TTL与队列设置TTL的区别)）中，介绍过如果给消息设置 TTL 属性，消息可能并不会按时“死亡”，因为 RabbitMQ 只会检查在队列头的那个消息是否过期，如果过期则丢弃到死信队列；**<font color="green">那么，当第一个消息的延时时长很长，而第二个消息的延时时长很短时，第二个消息也不会优先得到执行，而是需要等待在队列头的第一个元素被丢弃之后，才会轮到它。</font>**



#### c. 代码实现

​		为了方便，方案一、方案二直接糅合到一起进行实现。见代码↓

```java
@Configuration
public class QueAndExConfig {

    public static final String X_EXCHANGE = "X";
    public static final String QUEUE_A = "QA";
    public static final String QUEUE_B = "QB";
    public static final String QUEUE_C = "QC";
    public static final String Y_DEAD_LETTER_EXCHANGE = "Y";
    public static final String DEAD_LETTER_QUEUE = "QD";


    // 声明 xExchange
    @Bean("xExchange")
    public DirectExchange xExchange() {
        return new DirectExchange(X_EXCHANGE);
    }

    // 声明 yExchange
    @Bean("yExchange")
    public DirectExchange yExchange() {
        return new DirectExchange(Y_DEAD_LETTER_EXCHANGE);
    }

    //声明队列 A：ttl属性为 10s 并绑定到对应的死信交换机Y
    @Bean("queueA")
    public Queue queueA() {
        Map<String, Object> args = new HashMap<>(3);
        //声明当前队列绑定的死信交换机
        args.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        //声明当前队列的死信路由 key
        args.put("x-dead-letter-routing-key", "YD");
        //声明队列的 TTL
        args.put("x-message-ttl", 10000);
        return QueueBuilder.durable(QUEUE_A).withArguments(args).build();
    }

    // 队列A 绑定 X 交换机
    @Bean                              // 按照bean名进行注入
    public Binding queueaBindingX(@Qualifier("queueA") Queue queueA,
                                  @Qualifier("xExchange") DirectExchange xExchange) {
        return BindingBuilder.bind(queueA).to(xExchange).with("XA");
    }

    // 声明队列 B：ttl属性 为 40s 并绑定到对应的死信交换机Y
    @Bean("queueB")
    public Queue queueB() {
        Map<String, Object> args = new HashMap<>(3);
        //声明当前队列绑定的死信交换机
        args.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        //声明当前队列的死信路由 key
        args.put("x-dead-letter-routing-key", "YD");
        //声明队列的 TTL
        args.put("x-message-ttl", 40000);
        return QueueBuilder.durable(QUEUE_B).withArguments(args).build();
    }

    // 队列 B 绑定 X 交换机
    @Bean
    public Binding queuebBindingX(@Qualifier("queueB") Queue queue1B,
                                  @Qualifier("xExchange") DirectExchange xExchange) {
        return BindingBuilder.bind(queue1B).to(xExchange).with("XB");
    }

    // 声明死信队列 QD
    @Bean("queueD")
    public Queue queueD() {
        return new Queue(DEAD_LETTER_QUEUE);
    }

    // 死信队列 QD 绑定至 死信交换机Y
    @Bean
    public Binding deadLetterBindingQD(
        @Qualifier("queueD") Queue queueD,
        @Qualifier("yExchange") DirectExchange yExchange) {
        return BindingBuilder.bind(queueD).to(yExchange).with("YD");
    }

    // ==========================================================方案二添加配置
    // 声明队列 C
    @Bean("queueC")
    public Queue queueC(){
        Map<String, Object> args = new HashMap<>(3);
        //声明当前队列绑定的死信交换机
        args.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        //声明当前队列的死信路由 key
        args.put("x-dead-letter-routing-key", "YD");
        // 没有声明 TTL 属性！！！！！！！！！！！！
        // 需要在发送消息的时候，给消息指定TTL
        return QueueBuilder.durable(QUEUE_C).withArguments(args).build();
    }

    // 声明队列 C 绑定 X 交换机
    @Bean
    public Binding queuecBindingX(@Qualifier("queueC") Queue queueC,
                                  @Qualifier("xExchange") DirectExchange xExchange){
        return BindingBuilder.bind(queueC).to(xExchange).with("XC");
    }
}
```



- 消息生产者

```java
@RequestMapping("/ttl")
@RestController
public class SendMsgController {

    @Resource
    private RabbitTemplate rabbitTemplate;

    // http://localhost:8080/ttl/sendMsg/2323
    @GetMapping("/sendMsg/{message}")
    public void sendMsg(@PathVariable String message) {
        System.out.println("当前时间：" + new Date() + ", 发送一条信息给两个 TTL 队列：" + message);
        rabbitTemplate.convertAndSend("X", "XA", "消息来自 ttl 为 10S 的队列: " + message);
        rabbitTemplate.convertAndSend("X", "XB", "消息来自 ttl 为 40S 的队列: " + message);
    }

    
    // ==========================================================方案二发起请求    
    // http://localhost:8080/ttl/sendExpirationMsg/HelloOne/20000
    // http://localhost:8080/ttl/sendExpirationMsg/HelloTwo/2000
    @GetMapping("/sendExpirationMsg/{message}/{ttlTime}")
    public void sendMsg(@PathVariable String message, @PathVariable String ttlTime) {
        rabbitTemplate.convertAndSend("X", "XC", message, correlationData -> {
            // 设置消息TTL属性
            correlationData.getMessageProperties().setExpiration(ttlTime);
            return correlationData;
        });
        System.out.println("当前时间：" + new Date() + ", 发送一条TTL时长为" + ttlTime + "毫秒的信息给队列 C:" + message);
    }
}
```



- 消息消费者**（消费死信队列中的消息）**

```java
@Component
public class DeadLetterQueueConsumer {
    @RabbitListener(queues = "QD")
    public void receiveD(Message message, Channel channel) throws IOException {
        String msg = new String(message.getBody());
        System.out.println("当前时间：" + new Date() + ", 收到死信队列信息：" + msg);
    }
}
```





### 9.5 RabbitMQ依赖插件实现延时队列

*==**【在交换机处实现消息延迟】**==*

> ​		**针对于上述[方案二](#b.-方案二)，如果不能实现在消息粒度上的 TTL，并使其在设置的 TTL 时间 及时死亡，就无法设计成一个通用的延时队列。**



1. 插件下载（.ez结尾）

[rabbitmq-delayed-message-exchange](https://github.com/rabbitmq/rabbitmq-delayed-message-exchange)

```shell
# 将插件安装到plugins目录
/opt/rabbitmq_server-3.7.0/plugins

# 使插件生效
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```

**延时插件添加前后对比：**

![延时插件添加前后对比](imgs\延时插件添加前后对比.png)



2. 代码架构图

> ​		在我们自定义的交换机中，这是一种新的交换类型，该类型消息支持延迟投递机制 消息传递后并 不会立即投递到目标队列中，而是存储在 mnesia(一个分布式数据系统)表中，当达到投递时间时，才 投递到目标队列中。

![使用插件实现延时队列代码架构](imgs\使用插件实现延时队列代码架构.png)

3. 代码实现

- 配置类

> ​		在我们自定义的交换机中，这是一种新的交换类型，该类型下，消息支持延迟投递机制。消息传递后并 不会立即投递到目标队列中，而是存储在 mnesia(一个分布式数据系统)表中，当达到投递时间时，才投递到目标队列中。

```java
@Configuration
public class DelayedQueueConfig {

    public static final String DELAYED_QUEUE_NAME = "delayed.queue";
    public static final String DELAYED_EXCHANGE_NAME = "delayed.exchange";
    public static final String DELAYED_ROUTING_KEY = "delayed.routingkey";

    @Bean
    public Queue delayedQueue() {
        return new Queue(DELAYED_QUEUE_NAME);
    }

    //自定义交换机 我们在这里定义的是一个延迟交换机
    @Bean
    public CustomExchange delayedExchange() {
        Map<String, Object> args = new HashMap<>();
        // 自定义交换机的类型
        args.put("x-delayed-type", "direct");
        return new CustomExchange(DELAYED_EXCHANGE_NAME, "x-delayed-message", true, false, args);
    }

    @Bean
    public Binding bindingDelayedQueue(@Qualifier("delayedQueue") Queue queue,
                                       @Qualifier("delayedExchange") CustomExchange delayedExchange) {
        return BindingBuilder.bind(queue).to(delayedExchange)
            .with(DELAYED_ROUTING_KEY).noargs();
    }
}
```





- 消费者

```java
@Component
public class ReceiveDelayedQueue {

    public static final String DELAYED_QUEUE_NAME = "delayed.queue";

    @RabbitListener(queues = DELAYED_QUEUE_NAME)
    public void receiveDelayedQueue(Message message) {
        String msg = new String(message.getBody());
        System.out.println("当前时间："+ new Date() +", 收到延时队列的消息：" + msg);
    }
}
```





- 生产者

```java
@RestController
@RequestMapping("/ttl")
public class SendMsg {

    @Resource
    RabbitTemplate rabbitTemplate;

    public static final String DELAYED_EXCHANGE_NAME = "delayed.exchange";
    public static final String DELAYED_ROUTING_KEY = "delayed.routingkey";

    // http://localhost:8080/ttl/sendDelayMsg/come-on-baby1/20000
    // http://localhost:8080/ttl/sendDelayMsg/come-on-baby2/2000
    @GetMapping("/sendDelayMsg/{message}/{delayTime}")
    public void sendMsg(@PathVariable String message, @PathVariable Integer delayTime) {
        rabbitTemplate.convertAndSend(DELAYED_EXCHANGE_NAME, DELAYED_ROUTING_KEY, message,
                correlationData -> {
                    // 有了插件，就可以设置延时消息了。（单位：ms）
                    correlationData.getMessageProperties().setDelay(delayTime);
                    return correlationData;
                });
        System.out.println(" 当 前 时 间 ： "+ new Date() +", 发送一条延迟 "+ delayTime +" 毫秒的信息给队列 delayed.queue:" + message);
    }
}
```





# ==RabbitMQ保证消息可靠性==

> - 生产者到 RabbitMQ：事务机制、消息确认机制、return机制。(注意：事务机制和 Confirm机制是互斥的，两者不能共存，会导致 RabbitMQ报错。)
> - RabbitMQ自身：交换机持久化、队列持久化。集群、镜像模式。
> - RabbitMQ到消费者：消息手动应答、死信队列、消息补偿机制（requeue）。



## 一、消息应答

> ​		**<font color="red">消息应答就是：消费者在接收到消息并且处理该消息之后，告诉 rabbitmq 它已经处理了，rabbitmq 可以将此消息删除了。</font>**
>
> ​		值得注意的是：在使用<u>自动应答</u>还是<u>手动应答</u>时，我们需要在__高吞吐量__和**数据传输安全性**方面做权衡。
>
> -  自动应答的消息吞吐量很高，但是数据传输安全性比较低。
>
> -  手动应答的消息吞吐量较低，但是数据传输安全性很高。



### 1.1 消息自动应答

> ​		**消息只要被发送，就认为已经传送成功。（立马ack）**。内存中的消息将会被删除，而不管消息有没有被成功消费。

```java
boolean isAutoack = true;   // 自动应答
channel.basicConsume(QUEUE_NAME, isAutoack, deliverCallback, cancelCallback);
```



### 1.2 消息手动应答

​		正常情况下消息发送方发送的多个消息，会由消息消费者轮询的接收到消息并进行处理。但如果消息发送者发送了消息<u>“HelloWorld”</u>，按理来说需要一个消费者`consumerA`来处理该消息，但是由于`consumerA`处理消息的时间较长，**还未处理完（还没有执行 ack 代码）**的时候，`consumerA`就发生了宕机。此时将会观察到消息被另一个消费者`consumerB`接收到了。这说明消息<u>“HelloWorld”</u>被重新入队，然后被分配给能处理消息的`consumerB`处理了。

> ​		默认消息采用的是**<font color="blue">自动应答</font>**，所以我们要想实现**<font color="green">消息在消费过程中不产生丢失（这仅仅建立在RabbitMQ服务一切<font color="red">正常</font>的基础上。如果想在RabbitMQ服务发生了<font color="red">故障</font>的情况下仍然保证消息不丢失，就需要使用RabbitMQ持久化策略了）</font>**，需要把自动应答改为**<font color="red">手动应答</font>**。
>
> 
>
> - 消息消费者（需要修改一些参数）
>
> ```java
>public static void consumeMsg(){
>     // 声明接收消息的回调接口（消息接收成功之后，将会调用此回调方法）
>    DeliverCallback deliver = (consumerTag, message) -> {
>         System.out.println(new String("消费者接收消息：" + message.getBody()));
> 
>         // 修改2：手动消息应答
>         //                   消息的标记                           禁止批量应答
>         channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
>     };
> 
>     // 声明取消消息接收的回调接口（消息接收成功之后，此方法无用）
>     CancelCallback cancel = consumerTag -> {
>         System.out.println(consumerTag + ": 消费中断~");
>     };
> 
>     // 修改1：禁止自动消息应答
>     boolean isAutoack = false;   // 禁止自动应答
>     channel.basicConsume(QUEUE_NAME, isAutoack, deliver, cancel);
> }
> ```
> 
> 
> 
> - 消息生产者（未改动）
>
> ```java
>public static void sendMsg(String simpleMsg) {
>     // 获取连接
>    try (Connection connection = ConnectionUtil.getConnection()) {
> 
>         // 获取信道
>         //channel 实现了自动 close 接口 自动关闭 不需要显示关闭
>         Channel channel = connection.createChannel();
> 
>         // 使用Java程序创建一个队列
>         boolean isDurable = false;   // 指定队列不会持久化
>         channel.queueDeclare(QUEUE_NAME, isDurable, false, false, null);
> 
>         channel.basicPublish("", QUEUE_NAME, null, simpleMsg.getBytes());
> 
>         System.out.println("success---------------");
> 
>     } catch (IOException e) {
>         e.printStackTrace();
>     } catch (TimeoutException e) {
>         e.printStackTrace();
>     }
> }
> ```
> 



- 消息手动应答涉及到的方法

```java
// RabbitMQ 已经知道该消息已经成功的接收并被处理了，可以将其丢弃了。
channel.basicAck(用于肯定确认)

// 批量拒绝多条消息
channel.basicReject(用于否定确认)
    
// 拒绝单条消息    
channel.basicNack(用于否定确认)
```

**<font color="green">以上方法涉及到的参数解析</font>**

```java
/**   三种应答方法的参数说明
 * @param deliveryTag:long  表示所应答消息的标识符。每个消息都拥有一个唯一性的标识符。
 * @param multiple:boolean  手动应答时，必定为false，代表成功处理一个消息，咱才应答一次。
 *           true：代表批量应答 channel 上在当前deliveryTag前还未应答的消息
 *           false：只会应答当前所处理的消息
 * @param requeue:boolean
 *           true：RabbitMQ 会重新将这条消息存入队列，以便可以发送给下一个订阅的消费者
 *           false：RabbitMQ 立即会把消息从队列中移除，而不会把它发送给新的消费者。
 */
```



__图解multiply参数：__

<img src="imgs\图解multiply参数.png" alt="图解multiply参数" style="zoom: 50%;" />



**图解requeue参数：**

> ​		如果消费者由于某些原因**<font color="blue">失去连接</font>** **(其通道已关闭，连接已关闭或 TCP 连接丢失)**，导致消息未发送 ACK 确认，RabbitMQ 将了解到消息未完全处理，并将对其重新排队。如果此时其他消费者 可以处理，它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。

![图解requeue参数](imgs\图解requeue参数.png)



## 二、RabbitMQ事务

> ​		RabbitMQ事务指的是基于**<font color="red">客户端</font>**实现的事务管理，当在消息发送过程中添加了事务，处理效率会降低⼏⼗倍甚⾄上百倍。因为事务机制是**阻塞**的过程，**只有等待服务器回应之后才会处理下一条消息**，所以在实际应用中，极少使用事务去保证消息可靠性。而是使用*<u>【消息确认】与【return机制】。</u>*

```java
public class TransactionSender {

    private static final String ex_name = "ex_tx";
    private static final String q_name = "q_tx";
    private static final String rt_name = "rt_tx";

    public static void main(String[] args) throws IOException, TimeoutException {

        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        channel.exchangeDeclare(ex_name, BuiltinExchangeType.DIRECT);
        channel.queueDeclare(q_name, false, false, false, null);
        channel.queueBind(q_name, ex_name, rt_name);

        // 将当前信道设置成事务模式
        channel.txSelect();   
        try {
            channel.basicPublish(ex_name, rt_name, null, "hello".getBytes());
            //显示抛出RuntimeException
            int a = 1/0;
            // 用于提交事务
            channel.txCommit();
        } catch (IOException e) {
            e.printStackTrace();
            
            // 用于回滚事务
            channel.txRollback();
        }
    }
}
```





## 三、消息发布确认

***参考文档：***[如何保证消息的正确发送](https://www.jianshu.com/p/15f0c1a105fb?u_atoken=43a491ac-5677-474c-87ef-feb3715cd478&u_asession=01s_UD3Y1iPH5lkZeyDnITuXHr69k6Y1sNhYFj3UsI-F0q2uDzFA1dXQEgMBqk5WTlX0KNBwm7Lovlpxjd_P_q4JsKWYrT3W_NKPr8w6oU7K_pkqa5oX6S21RROfNu5ow5p6-k6uIK8qd-Te2lZdEIwmBkFo3NEHBv0PZUm6pbxQU&u_asig=05gkhrv9lRRcyrpeLqZfvWhy7INAqggqr1AbjTWV9wK2dzSYb0LN3d2jxY70cD-qorOnTpbx5rDjLu8cVGE9Ks1et6iJy0jXx4LC0pdXMnlvc1_hFOztpDtDJI67eHfg8co8iWZAPtHqOURnXAM3VTs98HZ3Y7u7T1MMskcVrfLuX9JS7q8ZD7Xtz2Ly-b0kmuyAKRFSVJkkdwVUnyHAIJzZEBGE1El8Lx9GcyMIRM0-wg7wKM8ECicNEtsacy69sO9ku645VqGlBHykiFwmnEve3h9VXwMyh6PgyDIVSG1W-4fY4XuJEBjIobZp7xgeAKcAODqQLQXD48KAlIfYJBkv21LbNMnLXjJf0VMzD4NIIPXJ8oSzGGsqBIZvmps0VtmWspDxyAEEo4kbsryBKb9Q&u_aref=gZTWTcz69kd%2Fj63ymJ%2BoiGXj3f0%3D)



> ​		如果Broker产生故障/错误，会导致消息直接丢失。在这种极端情况下仅仅依赖于**<u>消息手动应答</u>**是不能够完全保障消息可靠性的。
>
> ​		在Broker处的故障又分为**<font color="red">交换机故障</font>**和**<font color="red">队列故障</font>**，RabbitMQ分别提供了对应的解决方案来保证**<font color="blue">消息发布到队列</font>**期间的消息可靠性。即：**<u>*<font color="red">消息确认机制与Return机制。</font>*</u>**

|             RabbitMQ保证消息发布可靠性的解决方案             |
| :----------------------------------------------------------: |
| ![RabbitMQ消息可靠性多种解决方案](imgs\RabbitMQ消息可靠性多种解决方案.png) |



### 3.1 发布确认原理

​		<font color="blue"><u>生产者</u></font>将信道设置成 `confirm 模式`，一旦信道进入 confirm 模式，**所有在该信道上发布的消息都必须被指派一个唯一的 ID。**对于<font color="#75c4ff">消息确认机制</font>，无论消息是否到达<u>*交换机*</u>，监听<font color="green"><u>消息确认(confirm)结果</u></font>的回调接口都会被调用。而对于<font color="#75c4ff">Return机制</font>，只有当消息到达*<u>队列</u>*失败时，<font color="green"><u>Return回调接口</u></font>才会被调用。



### 3.2 发布确认-普通Maven工程实现

- 大前提：开启发布确认

  ​	发布确认默认是没有开启的，如果要开启需要调用方法 `confirmSelect`。**每当你要想使用发布确认，都需要在 channel 上调用该方法。**

  ```java
  Channel channel = connection.createChannel();
  channel.confirmSelect();
  ```

  

#### a. 单个确认发布

> ​		这是一种简单的确认方式，它是一种**同步确认发布**的方式，也就是发布一个消息之后只有它被确认发布**（只要被确认发布，就说明已经保存在了磁盘中）**，后续的消息才能继续发布，`waitForConfirmsOrDie(long)`这个方法只有在消息被确认的时候才返回true。
>
> ​		如果在指定的时间范围内这个消息没有被确认，那么它将抛出异常。 这种确认方式有一个最大的缺点就是：**<font color="red">发布速度特别的慢。</font>**因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能已经足够了。
>
> 
>
> > **优点：**
> >
> > ​	**当有消息发送出现问题时，可以及时的做出补救处理。**
> >
> > **缺点：**
> >
> > ​	**发送消息的速度慢，效率很低。**

```java
public static void singlePublishMessageIndividually() throws Exception {
    try (Connection connection = ConnectionUtil.getConnection()) {

        Channel channel = connection.createChannel();
        String queueName = UUID.randomUUID().toString();
        // 创建一个临时队列
        channel.queueDeclare(queueName, false, false, false, null);
        //开启发布确认
        channel.confirmSelect();

        long begin = System.currentTimeMillis();
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i + "";
            channel.basicPublish("", queueName, null, message.getBytes());

            // 服务端返回 false 或超时时间内未返回，生产者可以消息重发
            while (!channel.waitForConfirms()) {
                // 消息重发
                channel.basicPublish("", queueName, null, message.getBytes());
            }
        }
        long end = System.currentTimeMillis();
        // 发布100个单独确认消息,耗时3074ms
        System.out.println("发布" + MESSAGE_COUNT + "个单独确认消息,耗时" + (end - begin) + "ms");
    }
}
```





#### b. 批量确认发布

> ​		**单个确认发布方式的消息发送速率非常慢，如果可以先发布一批消息然后再一次性的进行消息确认就可以极大地提高消息吞吐量。**
>
> ​		当然这种方式的**缺点就是：当发生故障导致发布出现问题时，不知道是哪个消息出现 问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。**
>
> ​		**<font color="green">这种 方案仍然是同步的，也一样阻塞消息的发布。</font>**
>
> 
>
> **缺点：**
>
> ​	**当发生故障导致发布消息出现问题时，无法知道是哪个消息出现了问题。**
>
> ​	**（要想排除问题所在，必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。）**
>
> **优点：**
>
> ​	**消息发送速率快。**

```java
public static void multiPublishMessageBatch() throws Exception {
    try (Channel channel = ConnectionUtil.getConnection().createChannel()) {
        String queueName = UUID.randomUUID().toString();
        // 创建一个临时队列
        channel.queueDeclare(queueName, false, false, false, null);
        //开启消息发布确认
        channel.confirmSelect();

        //批量确认消息大小
        int batchSize = 100;
        //未确认消息个数
        int outstandingMessageCount = 0;

        long begin = System.currentTimeMillis();
        // 用来保存待确认的消息。如果出现了消息确认问题，可以重新发送此数组中的消息。直到成功发送为止
        String[] messages = new String[batchSize];   
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i + "";
            messages[outstandingMessageCount] = message;
            channel.basicPublish("", queueName, null, message.getBytes());
            outstandingMessageCount++;
            if (outstandingMessageCount == batchSize) {
                // 只要消息确认失败，就重新发送这一批消息。直到成功为止。
                while (!channel.waitForConfirms()) {    
                    for (int j = 0; j < outstandingMessageCount; j++) {
                        channel.basicPublish("", queueName, null, messages[j].getBytes());
                    }
                }
                outstandingMessageCount = 0;
            }
        }
        //为了确保还有剩余没有确认消息 再次确认
        if (outstandingMessageCount > 0) {
            // 只要消息确认失败，就重新发送这一批消息。直到成功为止。
            while (!channel.waitForConfirms()) {    
                for (int j = 0; j < outstandingMessageCount; j++) {
                    channel.basicPublish("", queueName, null, messages[j].getBytes());
                }
            }
        }
        long end = System.currentTimeMillis();
        // 发布100个批量确认消息，耗时57ms
        System.out.println("发布" + MESSAGE_COUNT + "个批量确认消息,耗时" + (end - begin) + "ms");
    }
}
```





#### c. 异步确认发布

> ​		异步确认虽然编程逻辑比上两个要复杂，但是性价比最高，无论是可靠性还是效率都没得说。
>
> ​		它是利用**回调函数**来达到消息可靠性传递的，这个中间件也是通过函数回调来保证是否投递成功， 下面就让我们来详细讲解异步确认是怎么实现的。
>
> 
>
> > ​		                                                **如何处理异步未确认消息?**
> >
> > ​		**<font color="red">最好的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列， 比如说用 ConcurrentLinkedQueue 这个队列在 `confirm callbacks回调接口` 与 `消息发布线程` 之间进行消息的传递。</font>**

```java
public static void unsyncPublishMessageAsync() throws Exception {
    try (Channel channel = ConnectionUtil.getConnection().createChannel()) {

        String queueName = UUID.randomUUID().toString();
        channel.queueDeclare(queueName, false, false, false, null);
        //开启发布确认
        channel.confirmSelect();

        /**
             * 线程安全有序的一个哈希表，适用于高并发的情况
             *     1.轻松的将序号与消息进行关联
             *     2.轻松批量删除指定的条目，只要给定需要删除的元素序列号
             *     3.支持并发访问
             */
        ConcurrentSkipListMap<Long, String> outstandingConfirms
            = new ConcurrentSkipListMap<>();

        /**                            确认收到消息的回调方法
             * @param sequenceNumber:long 消息序列号
             * @param multiple:boolean 是否批量发送的消息
             *        true：可以确认小于等于当前序列号的消息
             *        false：确认当前序列号消息
             */
        ConfirmCallback ackCallback = (sequenceNumber, multiple) -> {
            if (multiple) {
                //返回的是小于等于当前序列号的未确认消息 是一个 map
                ConcurrentNavigableMap<Long, String> confirmed =
                    outstandingConfirms.headMap(sequenceNumber, true);
                //清除该部分未确认消息
                confirmed.clear();
            } else {
                //只清除当前序列号的消息
                outstandingConfirms.remove(sequenceNumber);
            }
        };

        /**
             *                    消息接收失败的回调方法
             */
        ConfirmCallback nackCallback = (sequenceNumber, multiple) -> {
            String message = outstandingConfirms.get(sequenceNumber);
            System.out.println("发布的消息" + message + "未被确认，序列号" + sequenceNumber);
        };

        /**
             *                             添加一个异步确认的监听器
             * 1.确认收到消息的回调
             * 2.未收到消息的回调
             */
        channel.addConfirmListener(ackCallback, nackCallback);
        long begin = System.currentTimeMillis();
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = "消息" + i;
            /**
                 * channel.getNextPublishSeqNo()  获取下一个被发布的消息的序列号
                 * 通过序列号与消息体进行一个关联
                 * 全部都是未确认的消息体
                 */
            outstandingConfirms.put(channel.getNextPublishSeqNo(), message);
            channel.basicPublish("", queueName, null, message.getBytes());
        }
        long end = System.currentTimeMillis();
        // 发布100个异步确认消息,耗时10ms
        System.out.println("发布" + MESSAGE_COUNT + "个异步确认消息,耗时" + (end - begin) + "ms");
    }
}
```



#### d. 总结

> - 单个确认发布
>   	   同步等待确认，实现简单，但吞吐量非常有限。
>
> - 批量确认发布
>       批量同步等待确认，实现简单，可以设置合理的吞吐量。但一旦出现问题很难推断出是哪条消息出现了问题。
>
> - 异步确认发布
>       拥有最佳的性能和资源使用，在出现错误的情况下可以很好地控制，但是实现起来稍微难些。



> - 性能测试（simple）
>
> ![三种消息发布方式的耗时统计](imgs\三种消息发布方式的耗时统计.png)





### 3.3 发布确认-SpringBoot工程实现

#### a. 交换机处故障的解决方案

1. 实现消息失败重传概念架构图

**【保证消息可靠性-交换机处故障】**

> ​		使用一个类似于缓存机制的方法，当消息发送至交换机失败的时候，需要将消息重发。即回调接口。		下图为概念图，实际上缓存不需要我们考虑实现，我们只需要实现**交换机的回调接口ConfirmCallback**就可以了。
>
> ​		**对于这个接口，无论交换机是否成功的接收到消息，都会被调用：**
>
> - success：可以提示成功接收……
> - fail：可以将消息重发，这个消息被封装在**<font color="blue">CorrelationData</font>**中；也能获取到失败的原因。

![保证消息可靠性-交换机处故障](imgs\保证消息可靠性-交换机处故障.png)



2. 配置说明

**【保证消息可靠性-交换机处故障-代码架构图】**

![保证消息可靠性-交换机处故障-代码架构图](imgs\保证消息可靠性-交换机处故障-代码架构图.png)

- 配置文件

```yml
# 加上配置
spring:
  rabbitmq:
    publisher-confirm-type: correlated
```

**参数说明：**

- NONE
  禁用发布确认模式，是默认值。

- CORRELATED（本质上就是[异步确认发布](#c. 异步确认发布)）
  发布的消息成功到**交换机**后会触发回调方法。**<font color="blue">（属于异步确认，不会造成阻塞）</font>**

- SIMPLE（本质上就是[单个确认发布](#a. 单个确认发布)or[批量确认发布](#b. 批量确认发布)）

  经测试有两种效果：

  - 和 CORRELATED 值一样会触发回调方法
  - 在发布消息成功后使用 rabbitTemplate 调用 waitForConfirms 或 waitForConfirmsOrDie 方法 等待 broker 节点返回发送结果，根据返回结果来判定下一步的逻辑**<font color="blue">（属于同步确认，会造成阻塞）</font>**。要注意的是 waitForConfirmsOrDie 方法如果返回 false 则会关闭 channel，则接下来无法发送消息到 broker。



3. 代码实现

- 配置类：配置“交换机”、“队列”

```java
@Configuration
public class ConfirmConfig {
    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";

    //声明 Exchange
    @Bean("confirmExchange")
    public DirectExchange confirmExchange() {
        return new DirectExchange(CONFIRM_EXCHANGE_NAME);
    }

    // 声明确认队列
    @Bean("confirmQueue")
    public Queue confirmQueue() {
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    // 声明确认队列绑定关系
    @Bean
    public Binding queueBinding(@Qualifier("confirmQueue") Queue queue,
                                @Qualifier("confirmExchange") DirectExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("key1");
    }
}
```



- 消费者：消费队列中的消息（如果消息能成功到达队列的话）

```java
@Component
public class ConfirmConsumer {

    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";

    @RabbitListener(queues = CONFIRM_QUEUE_NAME)
    public void receiveMsg(Message message) {
        String msg = new String(message.getBody());
        System.out.println("接受到队列 confirm.queue 消息:" + msg);
    }
}
```



- 回调接口实现类

> ​		当交换机出现问题，无法接收到消息或者交换机没有问题，能够接收到消息，都会调用这个回调接口。

```java
@Component
public class MyCallBack implements RabbitTemplate.ConfirmCallback {
 	// 由于RabbitTemplate在Spring容器中只有一个实例，所以在此处配置回调接口后，在项目中的任何地方都可以使用
    @Resource
    private RabbitTemplate rabbitTemplate;

    // 在依赖注入完成后执行的方法
    @PostConstruct
    public void init() {
        // ConfirmCallback与ReturnCallback为RabbitTemplate中的内部接口：需要赋值到RabbitTemplate中才能被使用
        rabbitTemplate.setConfirmCallback(this);
    }
    
    /**
     * @param correlationData 与消息相关的数据
     * @param ack             交换机是否收到消息
     * @param cause           若交换机收到消息，cause为null。若交换机未收到消息，cause为失败的原因。
     * @description 【交换机】不管是否接收到消息，都会调用的一个回调方法
     * @author Jun Lu
     * @date 2022-10-04 12:22:17
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            System.out.println("交换机已经收到 id 为:" + id + "的消息");
        } else {
            // ！！！！！！！！！！！！！！！！！！！！！！！！！！！！
            // 消息发送失败至交换机，在这里可以进行消息重发……
            System.out.println("交换机还未收到 id 为:" + id + "消息,由于原因:" + cause);
        }
    }
}
```





- 提供者

```java
@RestController
@RequestMapping("/confirm")
public class ConfirmProducer {

    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";

    @Resource
    private RabbitTemplate rabbitTemplate;

    // ！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！！
    // 发消息时需要额外封装一个CorrelationData对象进行传递，代表当前消息。以便于被回调接口接收重传
    @GetMapping("/sendMessage/{message}")
    public void sendMessage(@PathVariable String message) {
        // 指定消息 id 为 1
        CorrelationData correlationData1 = new CorrelationData("1");
        String routingKey = "key1";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME, routingKey, message + routingKey, correlationData1);

        // 指定消息 id 为 2
        CorrelationData correlationData2 = new CorrelationData("2");
        routingKey = "key2";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME, routingKey, message + routingKey, correlationData2);
        System.out.println("发送消息内容:" + message);
    }
}
```





#### b. 队列处故障的解决方案

- 配置文件

```yml
# 加上配置
spring:
  rabbitmq:
    publisher-confirm-type: correlated
    publisher-returns: true  # 启用消息发布回退机制
```



- 修改回调接口实现类（实现ReturnCallback，设置mandatory 参数）

> - 当消息无法从交换机路由至队列时，ReturnCallback回调接口才会被调用。
>- 如果消息成功的由交换机路由至队列，ReturnCallback回调接口不会被调用。
> 
>> ​		在仅开启了生产者确认机制（只设置了ConfirmCallback回调接口）的情况下，交换机接收到消息后，会直接给消息生产者发送确认消息，如果发现该消息不可路由，那么消息会被直接丢弃，此时生产者是不知道消息被丢弃这个事件的。那么如何 让无法被路由的消息帮我想办法处理一下？最起码通知我一声，我好自己处理啊。
> >
> > ​		**通过设置 mandatory 参数可以在当消息传递过程中无法被路由时将消息返回给生产者。**

```java
@Component
public class MyCallBack implements RabbitTemplate.ConfirmCallback, RabbitTemplate.ReturnCallback {

    // 由于RabbitTemplate在Spring容器中只有一个实例，所以在此处配置回调接口后，在项目中的任何地方都可以使用
    @Resource
    private RabbitTemplate rabbitTemplate;

    // 在依赖注入完成后执行的方法
    @PostConstruct
    public void init() {
        // ConfirmCallback与ReturnCallback为RabbitTemplate中的内部接口：需要赋值到RabbitTemplate中才能被使用
        rabbitTemplate.setConfirmCallback(this);
        // 只有实现了ReturnCallback回调接口，才可以使用该参数
        // ！！！！！！！！！！！！！！！！！！！！
        // 不设置也没事，因为在SpringBoot项目中，如果我们实现了ReturnCallback接口，Mandatory将会被自动的设置为true
        rabbitTemplate.setMandatory(true);
        rabbitTemplate.setReturnCallback(this);
    }

    /**
     * @param correlationData 与消息相关的数据
     * @param ack             交换机是否收到消息
     * @param cause           若交换机收到消息，cause为null。若交换机未收到消息，cause为失败的原因。
     * @description 【交换机】不管是否接收到消息，都会调用的一个回调方法
     * @author Jun Lu
     * @date 2022-10-04 12:22:17
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            System.out.println("交换机已经收到 id 为:" + id + "的消息");
        } else {
            System.out.println("交换机还未收到 id 为:" + id + "消息,由于原因:" + cause);
        }
    }

    /**
     * @param message    失败路由的消息
     * @param replyCode  消息被退回的状态码
     * @param replyText  消息被退回的原因
     * @param exchange   交换机名
     * @param routingKey 路由key
     * @description      当消息无法【从交换机路由至队列】的时候的回调方法
     * @author Jun Lu
     * @date 2022-10-04 12:23:18
     */
    @Override
    public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
        System.out.println(" 消 息 " + new String(message.getBody()) +
                ", 被交换机 " + exchange + " 退回，退回原因 :" + replyText + ", 路 由 key:" + routingKey);
    }
}
```





## 四、备份交换机

### 4.1 简介

​		有了 mandatory 参数和消息回退接口处理无法被交换机传递的消息，我们获得了对无法投递消息的感知能力，有机会在生产者的消息无法被投递时进行处理。但有时候，我们并不知道该如何处理这些无法路由的消息，最多打个日志，然 后触发报警，再来手动处理。而通过日志来处理这些无法路由的消息是很不优雅的做法，特别是当生产者 所在的服务有多台机器的时候，手动复制日志会更加麻烦而且容易出错。而且**设置 mandatory 参数会增加生产者的复杂性，需要添加处理这些被退回的消息的逻辑。**

​		如果既不想丢失消息，又不想增加生产者的 复杂性，该怎么做呢？<u>前面在设置死信队列的文章中，我们提到，可以为队列设置死信交换机来存储那些 处理失败的消息，可是这些不可路由的消息根本没有机会进入到队列，因此无法使用死信队列来保存消息。</u> 在 RabbitMQ 中，有一种备份交换机的机制存在，可以很好的应对这个问题。

​		什么是备份交换机呢？备份交换机可以理解为 **RabbitMQ 中交换机的“备胎”**，当我们为某一个交换机声明一个对应的备份交换机时， 就是为它创建一个备胎。**当交换机接收到一条不可路由的消息时，不会丢弃该消息，而是会把这条消息转发至备份交换机中**，**由备份交换机来进行转发和处理**。通常备份交换机的类型为 **<font color="blue">Fanout</font>** ，这样就能把备份交换机中不可路由的消息都投递到与备份交换机绑定的队列中。当然，根据实际需求进行决定。

> ==**备份交换机优先级高。**==
>
> ​		**如果 mandatory 参数与备份交换机一起使用，消息将会被备份交换机接收；而不会被消息回退接口接收。**



### 4.2 架构demo

![备份队列demo](imgs\备份队列demo.png)



### 4.3 代码演示

> ​		要想实现备份交换机，只需在声明交换机时，给交换机指定`alternate-exchange`参数即可。参数值即为备份交换机的名称。

- 配置类：实现b中架构

```java
@Configuration
public class BackupExConfig {

    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";
    public static final String BACKUP_EXCHANGE_NAME = "backup.exchange";
    public static final String BACKUP_QUEUE_NAME = "backup.queue";
    public static final String WARNING_QUEUE_NAME = "warning.queue";

    // 声明确认队列
    @Bean("confirmQueue")
    public Queue confirmQueue() {
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    //声明确认队列绑定关系
    @Bean
    public Binding queueBinding(@Qualifier("confirmQueue") Queue queue,
                                @Qualifier("confirmExchange") DirectExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("key1");
    }

    // 声明备份 Exchange
    @Bean("backupExchange")
    public FanoutExchange backupExchange() {
        return new FanoutExchange(BACKUP_EXCHANGE_NAME);
    }

    // 声明确认 Exchange 交换机的备份交换机：设置alternate-exchange参数
    @Bean("confirmExchange")
    public DirectExchange confirmExchange() {
        ExchangeBuilder exchangeBuilder =
                ExchangeBuilder.directExchange(CONFIRM_EXCHANGE_NAME)
                        .durable(true)
                        // 设置该交换机的备份交换机
                        .withArgument("alternate-exchange", BACKUP_EXCHANGE_NAME);
        return exchangeBuilder.build();
    }

    // 声明警告队列
    @Bean("warningQueue")
    public Queue warningQueue() {
        return QueueBuilder.durable(WARNING_QUEUE_NAME).build();
    }

    // 声明报警队列绑定关系
    @Bean
    public Binding warningBinding(@Qualifier("warningQueue") Queue queue,
                                  @Qualifier("backupExchange") FanoutExchange
                                          backupExchange) {
        return BindingBuilder.bind(queue).to(backupExchange);
    }

    // 声明备份队列
    @Bean("backQueue")
    public Queue backQueue() {
        return QueueBuilder.durable(BACKUP_QUEUE_NAME).build();
    }

    // 声明备份队列绑定关系
    @Bean
    public Binding backupBinding(@Qualifier("backQueue") Queue queue,
                                 @Qualifier("backupExchange") FanoutExchange backupExchange) {
        return BindingBuilder.bind(queue).to(backupExchange);
    }
}
```



- 报警消费者

```java
@Component
public class WarningConsumer {
    public static final String WARNING_QUEUE_NAME = "warning.queue";

    @RabbitListener(queues = WARNING_QUEUE_NAME)
    public void receiveWarningMsg(Message message) {
        String msg = new String(message.getBody());
		System.out.println("报警队列发现不可路由的消息：" + msg);
    }
}
```



## 五、消息幂等性

[RabbitMQ消息幂等性](https://blog.csdn.net/BruceLiu_code/article/details/102803915)

- 定义

  ​		**<font color="red">消息幂等性：保证同一个消息不被消费者重复消费两次。就算当前消息被<u>接收</u>到n次，也只能被<u>消费</u>一次。（1^n == 1）</font>**

  

- 场景

​		举个例子，当消费者消费完消息之后，通常会发送一个ack应答确认信息给生产者，但是在此期间有可能因为网络中断等原因，导致生产者未能收到确认消息，由此这条消息将会被重复发送给其他消费者进行消费。但实际上这条消息已经被消费过了，这就是<u>重复消费</u>的问题。

​	



- 解决方案

  1. 给消息加上<u>全局ID</u>或者写个<u>唯一标识(如时间戳、UUID等)</u> 

  ​		每次消费消息之前根据消息id去判断该消息是否已消费过，如果已经消费过，则不处理这条消息；否则正常消费消息，并且进行入库操作。(消息全局ID作为数据库表的主键，防止重复)。

  2. 利用Redis的原子性 (推荐使用)

     利用 Redis 执行 `setnx` 命令，天然具有幂等性。从而实现不重复消费。





# RabbitMQ进阶

## 一、优先级队列

- 使用场景

  ​		在我们的系统中有一个订单催付的场景，我们的客户在天猫下的订单，淘宝会及时将订单推送给我们，如 果在用户设定的时间内未付款那么就会给用户推送一条短信提醒，很简单的一个功能对吧。但是，tmall 商家对我们来说，肯定是要分大客户和小客户的对吧，比如像苹果，小米这样大商家一年起码能给我们创造很大的利润，所以理应当然，他们的订单必须得到优先处理，而曾经我们的后端系统是使用 redis 来存 放的定时轮询，大家都知道 **redis 只能用 List 做一个简简单单的消息队列，并不能实现一个优先级的场景**，所以订单量大了后采用 RabbitMQ 进行改造和优化,如果发现是大客户的订单给一个相对比较高的优先级， 否则就是默认优先级。



- 注意事项

  ​		**要让队列实现优先级需要做的事情：**

  ​		**<font color="red">队列需要设置为优先级队列；消息需要设置消息的优先级；消费者需要等待消息已经发送到队列中才去消费。因为，这样才有机会对消息进行排序。</font>**<u>总而言之，只有当消息来不及处理的时候，考虑优先级才有意义。否则，如果来一条消息处理一条，并且可以迅速处理，压根都不繁忙。也就不需要考虑优先级问题了。反正都会轮到你呗。</u>





- 设置方法

  - *控制台设置优先级队列：*最大优先级范围0~255。但官方推荐**0~10，再高会影响性能！**

  ![控制台设置优先级队列](imgs\控制台设置优先级队列.png)

  <hr>

  - *程序中设置优先级队列*

  ```java
  Map<String, Object> params = new HashMap();
  params.put("x-max-priority", 10);
  channel.queueDeclare("hello", true, false, false, params);
  ```

  <hr>

  - 设置完后，管理界面显示具有**<font color="blue">pri</font>**标志

  ![优先级队列管理界面pri标志](imgs\优先级队列管理界面pri标志.png)

  <hr>

  - 给消息设置优先级

  ```java
  // 给消息赋予一个 priority 属性
   AMQP.BasicProperties properties 
       = new AMQP.BasicProperties().builder().priority(5).build();
  
  channel.basicPublish("", QUEUE_NAME, properties, message.getBytes());
  ```





## 二、惰性队列

1. 使用场景

​		RabbitMQ 从 3.6.0 版本开始引入了惰性队列的概念。惰性队列会尽可能的将消息存入磁盘中，而在消费者消费到相应的消息时才会被加载到内存中。**它的一个重要的设计目标是能够支持更长的队列，即支持更多的消息存储。**当消费者由于各种各样的原因(比如消费者下线、宕机亦或者是由于维护而关闭等)而致使长时间内不能消费消息造成堆积时，惰性队列就很有必要了。 

​		<u>*默认情况下*</u>，当生产者将消息发送到 RabbitMQ 的时候，队列中的消息会尽可能的存储在内存之中， 这样可以更加快速的将消息发送给消费者。<u>即使是需要持久化的消息，在被写入磁盘的同时也会在内存中驻留一份备份。</u>当 RabbitMQ 需要释放内存的时候，会将内存中的消息换页至磁盘中，这个操作会耗费较长的 时间，也会阻塞队列的操作，进而无法接收新的消息。虽然 RabbitMQ 的开发者们一直在升级相关的算法， 但是效果始终不太理想，尤其是在消息量特别大的时候。

​		**惰性队列会将接收到的消息直接存入文件系统中，而不管是持久化的或者是非持久化的。**这样可以减少内存的消耗，但是会增加I/O的使用。如果消息是持久化的，那么这样的I/O操作不可避免，惰性队列和持久化消息可谓是“最佳拍档”。注意如果惰性队列中存储的是非持久化的消息，内存的使用率会一直很稳定，但是重启之后消息一样会丢失。

​		==**总而言之，惰性队列就是将消息存放至磁盘中存储。而在内存中只保存该消息在磁盘中的索引。**==

<hr>

2. 配置方式（两种）

- **<font color="green">声明队列的时候设置参数</font>**
- **<font color="green">通过Policy方式进行设置</font>**

注意点：

- 如果一个队列同时使用这两种方式设置的话，那么 Policy 的方式具备更高的优先级。

- 如果要通过声明的方式改变已有队列的模式的话，那么只能先删除队列，然后再重新声明一个新的。

```java
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-queue-mode", "lazy");
channel.queueDeclare("myqueue", false, false, false, args);
```



<hr>



3. 惰性队列与默认队列的内存开销对比

![惰性队列与默认队列内存开销](imgs\惰性队列与默认队列内存开销.png)

​		**发送 1 百万条消息，每条消息大概占 1KB，普通队列占用内存是 1.2GB，而惰性队列仅仅 占用 1.5MB**





## 三、RabbitMQ集群

[(28条消息) Rabbitmq 集群](https://blog.csdn.net/yangshihuz/article/details/114120589)

