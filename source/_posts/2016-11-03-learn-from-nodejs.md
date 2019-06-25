---
layout: post
title: "NodeJs 全记录七"
date: 2016-11-01 14:03
categories: nodejs javascript
tags: nodejs express websocket
---



前端程序猿的第二春 使用老本行JS来构建服务器端的神器  NodeJs




## websocket

- websocket是一个协议，类似于TCP/IP、HTTP协议，但它们需要有socket辅助来提供给外部调用的API，而websocket本身就有一个 **WebSocket API**  

- 使用socket这样的网络协议APIs不会符合原始安全模型，而websocket则不会，它提供给H5应用TCP的方式却不会削弱网络安全且还能同时拥有现代API  

- websocket相对于http协议，拟补了http不适合**实时通信**的缺陷，websocket以异步双向通信模式，通过传输层协议使其更急有灵活性  

- websocket协议能够让客户端和远程服务器端通过web建立**全双工通信**，且支持二进制字符串和文本字符串，协议包含打开**握手**之后的基本消息框架  


### websocket API

- websocket的接口，可通过它来建立**全双工通道**来收发消息，当连接远程服务器，只需创建一个websocket实体，即实例化一个实例，并传入类似 "ws://www.xxx.com" 的URL  
- 客户端和服务端在开始握手的期间，http会升级至websocket，这样建立了连接，其底层都是TCP协议  

- websocket api 是**纯事件驱动**的，即可使用异步事件监听来连接其整个生命周期的每个阶段  

`onopen()`  
一旦服务器响应websocket连接请求后触发open事件，这意味着协议握手结束，websocket已准备好收发数据，也就是说现在可确定服务端已经处理好了建立连接的请求，同意和你的应用通信了  

`onmessage()`  
一旦websocket收到数据后触发message事件  

```
<script>
    // ...
    ws.onmessage = function(e){
        if(e.data){
            console.log(e.data);
        }
    }
</script>
```

`onerror`  
连接失败或其它操作引起错误等都会触发error事件，从而导致连接关闭  

`onclose`  
连接关闭时触发close事件，之后客户端和服务器就不能再通信了  

- 建立其全双工连接，服务器发送数据或资源给客户端，它能自动发送状态改变的**数据和通知**，从而我们无须为了状态的更新而去轮询server，只要在客户端进行监听即可  

- websocket对象的实例有两个方法：`send()`  `close()`  

`send()`  
提供给服务端和客户端相互发送消息的方法，但需要在 `onopen` 连接完全建立了才能使用该方法  

```
<script>
    // ...
    ws.onopen = function(e){
        ws.send(str);
    }
</script>
```

`close()`  
手动关闭连接，一旦使用该方法，将不能再发送数据  

`ws.close(status, "reason")`  可选两参数，状态码和关闭原因  

```
<script>
    ws.close(
        1000,
        "Closing normally!"
    )
</script>
```

- websocket对象有三个属性：readyState、bufferedAmount、Protocol

`ws.readyState`  
该属性来表示连接状态，会随着连接状态变化而变化  

| 属性 | 值 | 状态 |
| :------------------- | ----: | :------------------: |
| WebSocket.CONNECTING | 0     | 连接正进行但未建立   |
| WebSocket.OPEN       | 1     | 连接已建立可发送消息 |
| WebSocket.CLOSING    | 2     | 正关闭握手           |
| WebSocket.CLOSED     | 3     | 连接已关闭或不能打开 |

```
<script>
    // ...
    if(ws.readyState === WebSocket.OPEN){
        // 待连接已建立
        ws.send(data);
    }
</script>
```

`ws.bufferedAmount`  
`send()`发送的数据不会立即传输，而是被浏览器缓存每次应用流出的数据，bufferedAmount可用来检查每次流出的数据大小，而这些数据已进入队列但未被传输  

```
<script>
    var ws = new WebSocket("ws://echo.websocket.org"),
        maxData = 10240;
    ws.onopen = function(){
        setInterval(function(){
            if(ws.bufferedAmount < maxData){
                ws.send()
            }
        },1000);
    }
</script>
```

`ws.protocol`  
返回当前使用的websocket协议，由服务器提供  


