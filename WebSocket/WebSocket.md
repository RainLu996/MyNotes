# WebSocket

## 一、 WebSocket与HTTP对比

- WebSocket是一种**<font color="blue">网络通信协议</font>**。RFC6455定义了它的通信标准。WebSocket是HTML5开始提供的一种在单个`TCP连接`上进行`全双工通讯`的**协议**。**<font color="red">浏览器和服务器之间只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。</font>**

- **HTTP协议是一种无状态的、无连接的、单向的应用层协议**，它采用了`请求/响应`模型。所以弊端也很明显，<u>通信请求只能由客户端发起，服务端只负责对请求做出应答；而不能实现服务端主动向客户端发起请求</u>。由于这种**单向请求**的特点，注定了如果服务器有连续的状态变化，而客户端想要获知就非常麻烦。大多数Web应用程序将通过频繁的异步AJAX请求实现长轮询。轮询的效率低，非常浪费资源（**<font color="green">因为必须不停连接，或者HTTP连接始终打开</font>**）。



[(121条消息) websocket与http优缺点_websocket和轮询的优缺点_PosterLiu的博客-CSDN博客](https://blog.csdn.net/qq_43100883/article/details/97646139)



- 轮询Demo

```java
var oid = this.orderInfo.orderId;
var i = setInterval(function () {
    console.log("-----");
    var url = "http://127.0.0.1:8080/" + "order/status/" + oid;
    axios({
        url: url,
        method: "get",
        headers: {
            token: getCookieVal("token")
        }
    }).then((res) => {
        if (res.data.data == "2") {
            // ……
            clearInterval(i);
        }
    });
}, 1000);
```





## 二、WebSocket与HTTP请求图示

- **http请求示意图**

<img src="imgs\http请求示意图.png" alt="http请求示意图" style="zoom:67%;" />

- **WebSocket请求图示**

<img src="imgs\websocket请求示意图.png" alt="http请求示意图" style="zoom:67%;" />



## 三、WebSocket协议

>  此协议分为两部分：握手和数据传输。握手是基于HTTP协议的。

- 来自客户端的握手看起来像如下形式：

```properties
GET ws://localhost:80/chat HTTP/1.1
Host: localhost
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dgh1ihnhbxbszsbub25jzq==
Sec-WebSocket-Extensions: permessage-deflate
Sec-WebSocket-Version: 13
```



- 来自服务器的握手看起来像如下形式：

```properties
HTTP/1.1 101 Switching Protocols
Upgrade: WebSocket
Connection: Upgrade	
Sec-WebSocket-Accept: s3pplmbitxaq9kygzzhzrbk+Xoo=
Sec-webSocket-Extensions: permessage-deflate
```



![字段说明](imgs\字段说明.png)



## 四、WebSocket实现

>  ​	实现 WebSocket 的web浏览器将通过 WebSocket对象公开所有必需的客户端功能（主要指支持Html5）的浏览器。

​	

### 4.1 客户端（浏览器）实现

```html
// 创建 Websocket对象
var webSocket = new WebSocket(URL);
```

> 参数：URL格式说明
>
> ​		`ws://ip地址:port/资源名称`



- WebSocket事件

![WebSocket对象相关事件](imgs\WebSocket对象相关事件.png)

```html
//前端发送websocket连接请求：请求发送完毕，将会建立一个长连接。
var webSocketUrl = "ws://127.0.0.1:8080/" + "websocket/" + this.orderInfo.orderId;
var websocket = new WebSocket(webSocketUrl);

//只要后端通过websocket向此连接发消息就会触发onmessage事件
websocket.onmessage = function(event) {
    var msg = event.data;
    console.log(msg);
}

// -----------------------------------------------------
// 前端发送数据给后端
websocket.send(data);
```



- WebSocket方法

| 方法    |                描述                 |
| ------- | :---------------------------------: |
| send(); | 前端向后端使用WebSocket连接发送数据 |



### 4.2 服务端实现

​		Tomcat的7.0.5版本开始支持 WebSocket，并且实现了 Java WebSocket规范(JSR356)。

​		WebSocket应用由一系列的 WebSocket Endpoint组成。 **Endpoint就是一个java对象，代表 WebSocket链接的一端，对于服务端，我们可以视为处理具体WebSocket消息的接口。**

<hr>

​		我们可以通过两种方式定义 Endpoint：

- 第一种是**编程式**：即继承类 `javax.websocket.Endpoint`并实现其方法。
- 第二种是**注解式**：即定义一个POJO，并添加 `@ServerEndpoint`注解。注解的value值，即为服务端`endpoint的URI`。

<hr>

​		**Endpoint实例在 WebSocket握手时创建，并在客户端与服务端链接过程中有效，最后在链接关闭时结束。**

​		在 Endpoint接口中明确定义了与其生命周期相关的方法，规范实现者确保生命周期的各个阶段调用实例的相关方法。生命周期方法如下：

![Endpoint接口中的生命周期方法](imgs\Endpoint接口中的生命周期方法.png)





>  **服务端如何接收客户端发送的数据呢？**
>
> ​		通过为 Session添加 `MessageHandler`消息处理器来接收消息，当采用注解方式定义Endpoint时，我们还可以通过`@OnMessage`注解指定接收消息的方法。
>
> **服务端如何推送数据给客户端呢？**
>
> ​		发送消息则由`RemoteEndpoint`完成，其实例由 Session 维护。
>
> ​		根据使用情况，我们可以通过`Session.getBasicRemote`获取**同步消息发送的实例**，然后调用其 `sendXXX()`方法就可以发送消息。还可以通过`session. getAsyncRemote`获取异步消息发送实例。





- Endpoint 结点类

```java
@Component
/*
    @ServerEndpoint注解是一个类层次的注解，它的功能主要是将目前的类定义成一个websocket服务器端,
    注解的值将被用于监听用户连接的终端访问URL地址，客户端可以通过这个URL来连接到WebSocket服务器端。
*/
@ServerEndpoint("/websocket/{oid}")
public class WebSocketServer {

    // 每一个客户端连接，在服务端都会对应一个Endpoint。
    // 为了标识不同的客户端，以便进行通信，需要使用map集合进行 Client-Endpoint 对应关系的存储。
    // 这个Map是一个全局的map（定义为static的，为所有对象共有），是用来存储当前服务端的所有Endpoint对象的。
    private static final ConcurrentHashMap<String, WebSocketServer> endpointMap = new ConcurrentHashMap<>();

    // 声明session对象，通过该对象可以发送消息至客户端
    private Session session;

    /**
     * @description 当前端请求建立websocket连接时，会执行OnOpen注解的方法
     * @author Jun Lu
     * @date 2022-08-11 15:38:34
     */
    @OnOpen
    public void open(@PathParam("oid") String orderId, Session session) {
        this.session = session;     // 当前客户端对应在服务端endpoint的session赋值给当前endpoint
        endpointMap.put(orderId, this);     // 将“客户端”注册到大map中
    }

    /**
     * @description 前端关闭⻚⾯或者主动关闭websocket连接，都会执⾏close
     * @author Jun Lu
     * @date 2022-08-11 15:38:34
     */
    @OnClose
    public void close(@PathParam("oid") String orderId){
        endpointMap.remove(orderId);
    }

    /**
     * @description 当连接建立过程中出现了异常，将会调用此方法。
     *              注意：当方法被加上了@OnError注解，那么方法中的参数列表必须加上 Throwable 参数！！！
     * @author Jun Lu
     * @date 2022-10-06 22:31:34
     */
    @OnError
    public void onError(Session session, Throwable error){

    }

    /**
     * @description 封装专门推送消息的方法
     * @author Jun Lu
     * @date 2022-08-11 15:49:03
     */
    public static void sendMsg(String orderId, String msg) {
        Session session = endpointMap.get(orderId).session;
        try {
            session.getBasicRemote().sendText(msg);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * @description 收到客户端的消息后调用的方法。（在客户端使用send方法向后端发送消息）
     * @author Jun Lu
     * @date 2022-10-06 22:52:56
     */
    @OnMessage
    public void onMessage(String message) {
        if(message.equals("ping")) {
            this.session.getAsyncRemote().sendText("pang");
        }
    }
}
```





- WebSocket配置类

```java
@Configuration
public class WebSocketConfig {

    @Bean
    /**
     * 会自动注册使用了@ServerEndpoint注解声明的Websocket Endpoint。
     * 要注意，如果使用的是独立的Servlet容器，而不是SpringBoot的内置容器，
     * 就不要注入ServerEndpointExporter。因为它将由Servlet容器提供和管理。
     */
    public ServerEndpointExporter getServerEndpointExporter() {
        return new ServerEndpointExporter();
    }

}
```

