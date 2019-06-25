---
layout: post
title: "NodeJs 全记录八"
date: 2016-11-18 18:17
categories: nodejs javascript
tags: nodejs express socket.io
---



前端程序猿的第二春 使用老本行JS来构建服务器端的神器  NodeJs




## Socket.IO
基于WebSocket出现的问题，Socket.IO在解决问题的同时保留了其API，也提供了良好的灵活性  

### 兼容性高
Socket.IO的消息传递是**基于传输**的，并非全依靠WebSocket，可在大部分浏览器和设备上运行，包括IE6到IOS  

### 可靠事件机制
提供对**超时**支持，监听 `connect` 和 `disconnect` 事件而不是 `open` 和 `close` 事件  

### Web通信方式
典型通信方式经HTTP收发资源，而**实时**Web世界中，都是基于**事件传输**的  

Socket.IO通过分发 (`emit`) 和 监听 (`listen`) 来进行JSON数据收发  

WebSocket.IO方式  

```
<script>
    // ...
    ws.on('connection', function(socket){
        socket.on('message', function(msg){
            // deal with msg
        });
    });
</script>
```

Socket.IO方式  

```
<script>
    // ...
    io.sockets.on('connection', function(socket){
        socket.on('message', function(msg){
            // deal with msg
        });
    });
</script>
```

### 命名空间
命名空间即多路传输，socket.io对同样的资源进行**频道划分**，并将数据传输给对应的回调，且允许监听多个命名空间的 `connection` 事件  

```
<script>
    // ...
    io.sockets.on('connection');
    io.of('/some/namespace').on('connection');
    io.of('/some/other/namespace').on('connection');
</script>
```

### 实现实时聊天

1. 引入express和socket.io模块声明为 `sio`
2. express创建服务并将返回对象 `app` 给 `sio` 监听
3. 调用监听返回的全局对象 `io` 的 `sockets` 属性
4. 用来监听 `connection`，回调带socket参数
5. 该参数在连接后，用来监听客户端分发的 `join` 用户加入的事件
6. 监听客户端分发的数据事件 `text`
7. 在 `join` 事件回调中，`broadcast.emit()` 给**所有用户**分发 `annoucement` 事件
8. 在客户端调用全局对象的 `connect()` 方法
9. 调用返回的对象 `socket` ，用来监听 `connect` 事件
10. 监听后回调中，使用 `socket` 来分发 `emit` 事件和给回调的参数
11. 当客户端有用户加入时，在 `connect` 回调中分发事件 `join`
12. 监听服务端分发的 `annoucement` 事件
13. 当用户提交消息时，分发 `text` 事件和给回调的参数