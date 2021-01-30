---
title: Spring Boot WebSocket
tags: SpringBoot
---

## Spring Boot WebSocket

利用 WebSocket，实现简单的客户端与服务端建立长连接并互发送文本消息



### 引入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
```



### 服务端

继承 WebSocketHandler 的实现类，并重写四个其中的方法

![image-20210131015817511](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210131015817511.png)

```java
import org.springframework.stereotype.Component;
import org.springframework.web.socket.CloseStatus;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;
import java.util.Scanner;

@Component
public class WebSocketInterceptor extends TextWebSocketHandler {
    @Override
    //和客户端链接成功的时候触发该方法
    public void afterConnectionEstablished(WebSocketSession session) {
        System.out.println("建立连接");
    }

    @Override
    //和客户端连接失败的时候触发该方法
    public void handleTransportError(WebSocketSession session, Throwable exception) throws Exception {
        session.close(CloseStatus.SERVER_ERROR);
        System.out.println("出现错误");

    }

    @Override
    //和客户端断开连接的时候触发该方法
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
        super.afterConnectionClosed(session, status);
        System.out.println("关闭连接");
    }

    @Override
    //和客户端建立连接后，处理客户端发送的请求
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        // 获取到客户端发送过来的消息
        String messagePayload = message.getPayload();
        System.out.println("用户："+session.getId()+"  "+messagePayload);
        String getMessages = getMessage();
        session.sendMessage(new TextMessage(getMessages));
        System.out.println("已发送给客户端");

    }
    private Scanner scanner=new Scanner(System.in);

    private String getMessage(){
        String messages = scanner.nextLine();
        if(messages==null || messages==""){
            System.out.println("请重新输入：");
            getMessage();
        }
        return messages;
    }

}
```



### 注册

```java
@Configuration
//开启WebSocket
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

    @Autowired
    WebSocketInterceptor webSocketInterceptor;

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        //客户端通过路径 /websocket 和服务端连接通信时，会使用 webSocketInterceptor 来处理会话
        //通信的客户端是通过 SockJS 实现的
        registry.addHandler(webSocketInterceptor,"/websocket").withSockJS();
    }
}

```



### 客户端

有关 SockJs 详情，请点击 [这里 ](https://github.com/sockjs/sockjs-client)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>WebSocket客户端</title>
    <script src="https://cdn.bootcss.com/sockjs-client/0.3.4/sockjs.min.js"></script>
    <link href="https://cdn.bootcss.com/twitter-bootstrap/4.4.1/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<style>
    .jumbotron {
        width: 100%;
    }

    #text {
        height: 3rem;
        font-size: 1rem;
        line-height: 3rem;
        margin: 1rem;
    }

    .btn {
        margin-right: 5px;
    }

    #connect {
        margin-left: 1rem;
    }

    #log {
        margin: 1rem 0 0 1rem;
    }

</style>
<div class="container">
    <div class="row">
        <div class="jumbotron">
            <input type="text" placeholder="请输入你想传输的内容" id="text" class="col-lg-12"/>
            <input type="button" value="连接" class="btn btn-info" id="connect" onclick="connect()"/>
            <input type="button" value="发送" class="btn btn-success" id="sent" disabled="disabled" onclick="sent()"/>
            <input type="button" value="断开" class="btn btn-danger" id="disconnect" disabled="disabled"
                   onclick="disconnect()"/>

            <div id="log">
                <p>聊天记录:</p>
            </div>
        </div>
    </div>
</div>
<script type="text/javascript">
    let text = document.querySelector('#text');
    let connectBtn = document.querySelector("#connect");
    let sentBtn = document.querySelector("#sent");
    let disconnectBtn = document.querySelector("#disconnect");
    let logDiv = document.querySelector("#log");

    let ws = null;

    function connect() {
        let targetUri = "/websocket";
        ws = new SockJS(targetUri);
        ws.onopen = function () {
            setConnected(true);
            log('和服务端连接成功！');
        };
        ws.onmessage = function (event) {
            log('服务端说：' + event.data);
        };
        ws.onclose = function () {
            setConnected(false);
            log('和服务端断开连接！')
        }
    }

    function sent() {
        if (ws != null) {
            ws.send(text.value);
            log('客户端说：' + text.value);
        } else {
            log('请先建立连接！')
        }
    }

    function disconnect() {
        if (ws != null) {
            ws.close();
            ws = null;
        }
        setConnected(false);
    }

    function log(value) {
        let content = document.createElement('p');
        content.innerHTML = value;
        logDiv.appendChild(content);
        text.value = '';
    }

    function setConnected(connected) {
        connectBtn.disabled = connected;
        disconnectBtn.disabled = !connected;
        sentBtn.disabled = !connected;
    }
</script>
</body>
</html>
```



### 测试

![image-20210131015259618](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210131015259618.png)

![image-20210131015317161](https://raw.githubusercontent.com/spviancc/spviancc.github.io/master/assets/image-20210131015317161.png)