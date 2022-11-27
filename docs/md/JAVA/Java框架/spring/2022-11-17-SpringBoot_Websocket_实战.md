## 什么是Websocket
>Websocket 是一种在单个TCP连接上进行全双工通信的协议。WebSocket连接成功后，服务端与客户端可以双向通信。在需要消息推送的场景，Websocket 相对于轮询能更好的节省服务器资源和带宽，并且能够更实时地进行通讯。
![](https://cdn.acwing.com/media/article/image/2022/11/22/192601_e93906406a-kob88.png)

 - 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种
 - HTTP 代理服务器。
 - 依赖于TCP协议
 - 数据格式比较轻量，性能开销小，通信高效。
 - 可以发送文本，也可以发送二进制数据。
 - 没有同源限制，客户端可以与任意服务器通信。
 - 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。
### SpringBoot 中使用 Websocket
在简单了解Websocket 之后，我们来动手实践一下。SpringBoot 中有多种方式可以实现Websocket Server，这里我选择使用Tomcat 中 javax.websocket.server 的api来实现
引入Maven依赖
```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
```
创建一个Bean用于处理Websocket 请求，通过ServerEndpoint 声明当前Bean 接受的Websocket URL
这里为什么声明的是 @Controller，后文会解释
```
import org.springframework.stereotype.Controller;

import javax.websocket.*;
import javax.websocket.server.ServerEndpoint;

@ServerEndpoint(value = "/message_websocket")
@Controller
public class MsgWebsocketController {

    @OnOpen
    public void onOpen(Session session) {
        // 先鉴权，如果鉴权通过则存储WebsocketSession，否则关闭连接，这里省略了鉴权的代码 
        WebSocketSupport.storageSession(session);
        System.out.println("session open. ID:" + session.getId());
    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose(Session session) {
        System.out.println("session close. ID:" + session.getId());
    }

    /**
     * 收到客户端消息后调用的方法
     */
    @OnMessage
    public void onMessage(String message, Session session) {
        System.out.println("get client msg. ID:" + session.getId() + ". msg:" + message);
    }

    /**
     * 发生错误时调用
     */
    @OnError
    public void onError(Session session, Throwable error) {
        error.printStackTrace();
    }

}
```
声明 ServerEndpointExporter
```
@Configuration
public class WebsocketConfig {

    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }

}
```
至此，Websocket Server 已经搭建完成，客户端已经可以和服务端通信了

服务端 向客户端推送消息 通过 session.getBasicRemote().sendText(message); 即可

### 建立一个SessionManager
当我们想向客户端推送消息的时候，首先我们需要找到客户端与服务端建立的连接，也就是WebscoketSession

WsServerContainer 中虽然已经存储了 WebscoketSession，但是并没有办法直接通过SessionId，或者我们的业务Id 直接定位到指定的Session，所以我们需要实现一个自己的SessionManager
```
final ConcurrentHashMap<Object, Session> sessionPool = new ConcurrentHashMap<>();
```
使用 ConcurrentHashMap 管理即可
### 分布式推送解决
![](https://cdn.acwing.com/media/article/image/2022/11/22/192601_eec677286a-ws1.png)
如图，用户1与服务器A建立Webscoket，用户2与服务器B建立Webscoket，那么用户1如果想向用户2推送一条消息，该如何实现？

WebscoketSession 实际上是网络连接，并不像我们传统应用的Session可以序列化到Redis，只能每个服务器管理自己的WebscoketSession，所以此时服务器A通知服务器B，你要给用户2推送一条消息。

一个比较简单有效的实现方法，利用消息队列，如下图
![](https://cdn.acwing.com/media/article/image/2022/11/22/192601_f40ab98c6a-ws2.png)
这个方案优点是实现简单，缺点是每台服务器都需要判断一遍当前是否存在指定的WebscoketSession ，方案细化的话则需要维护用户Session与每台服务器的关系，这样直接将消息推送给指定服务器即可