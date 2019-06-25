---
layout: post
title: "NodeJs 全记录四"
date: 2016-10-23 22:42
categories: node javascript
tags: node nodejs 
---



前端程序猿的第二春 使用老本行JS来构建服务器端的神器  NodeJs
![HTTP&TCP&IP](http://www.blogjava.net/images/blogjava_net/amigoxie/40799/o_http%e5%8d%8f%e8%ae%ae%e5%ad%a6%e4%b9%a0-11.jpg)





## 前言
当在传输数据时，可以单单只是用TCP/IP协议，但无法看到传输的数据，这样这些数据就没什么意义了，一旦有了应用层协议，就能识别数据内容，使其变得有意义，而应用层协议有HTTP、FTP、TELNET，等等  

## TCP
TCP传输控制协议，HTTP、SMTP、SSH等协议都是基于此协议，它能将数据从一台计算机传输至另一台机器，且保持数据一致性和顺序  

各种数据库及与数据库通信使用的都是TCP套接字  

### TCP特性
首要特性是面向连接的  

因各种的网络应用使用的都是TCP/IP协议，但基于数据包传输的IP，其数据包是独立传输且无序送达  

当TCP/IP和服务器建立连接时，为了让数据包送达有序，因发送的IP数据包包含了标识该连接及数据流顺序的信息  

用node写的服务器，只需考虑其连接及往套接字写数据即可，接收方会按序接收信息  

## Telnet
一种TCP协议上层的协议

### 用作访问node服务器的客户端
Telnet用来访问node写的服务器  

```
<script>
    // # server.js
    require('net').createServer(function(conn){
        // 每次有新连接建立
        // 都会执行一次回调
        console.log('new connection');
        // createServer的回调接收的一个参数
        // conn即net.Stream流对象可读也可写(类似process.stdout.write)
        conn.write(
            'welcome to node'
        );
        conn.on('data', function(data){
            // 监视客户端输入数据的变化
        });
        conn.on('close', function(){
            // 监视客户端状态(关闭)
        });
    }).listen(8080);
</script>
```
命令终端1输入`node server.js`  启动服务器  
命令终端2输入`telnet 127.0.0.1 8080`  终端二将收到 *welcome to node*  
命令终端1将收到信息  *new connection*  


## HTTP
超文本传输协议，是构建在请求与响应上  

而在node中，http.ServerRequest及http.ServerResponse来实现  

### server & client

在http模块中，提供http.server()和http.request()两静态方法，分别实例化  


## http.server()

```
<script>
    require('http')
        .server()
        .on('request', function(req,res){

        }).listen(80);
</script>
```

等价于  

```
<script>
    require('http')
        .createServer(function(req, res){

    }).listen(80);
</script>
```

### 请求过程
访问网站时，用户代理即客户端浏览器将会创建一个请求，该请求会经TCP发送给Web服务器，接着服务器做出响应  


#### 请求和响应

- 请求分为请求头(req.headers)和请求体(无)

```
<script>
    require('http').createServer(function(req, res){});
</script>
```

回调函数中的两参数，req和res，分别是http.ServerRuest和http.ServerResponse的实例  
- req为request客户端请求，提供了3个事件  

`data`  请求体数据到达该事件触发，事件提供一个参数chunk，分块处理数据  
`end`  请求体数据完成传输该事件触发，表示此后将不会有数据到来  
`close`  请求结束该事件触发  

```
<script>
    // ...
    req.on('data', function(chunk){
        // 监视请求数据变化
        // 数据已数据块分块存储
        // 并分块发送
        body += chunk;
    });
    req.on('end', function(){
        // end事件被触发
        // 即数据已接收完全
        // 接着向服务器发送数据
        res.writeHead(200, {
            'Content-Type': 'text/html'
        });
        // querystring.parse(str)  解析字符串为对象
        res.end('<p>your name is <b>' + qs.parse(body).name + '</b></p>');
    });
</script>
```

- res为response服务器响应，有三个重要成员  
*响应头*  `response.writeHead(状态码, {响应头每个属性})`  

*响应内容*  `response.write(data, [encoding])`  `data`为一个buffer(单字节)或字符串，当为字符串需指明其编码方式`encoding`默认utf-8，在`response.end`调用前，`reponse.write`可被多次调用  

*结束请求*  通知客户端所有发送已完成，被调用一次，不调用的话客户端永处于等待状态  

```
<script>
    // ...
    res.writeHead(200, {
        'Content-Type': 'text/html'
    });
    res.write('响应内容')
    res.end('结束响应');
</script>
```

### 头信息

```
<script>
    require('http').createServer(function(req, res){
        res.writeHead(200, {
            // 指明头信息
            // 文档类型默认text/plain
            'Content-Type': 'text/html'
        });
        // 由end写入数据时 node会结束响应
        res.end('Hello <b>World</b>');
    }).listen(8080);
</script>
```

### 块数据

```
<script>
    // ...
    res.write('Hello ');
    setTimeout(function(){
        res.end('<b>World</b>');
    }, 500);
</script>
```

先写入*Hello*，再延迟500毫秒写入World并结束响应  

node允许以数据块形式往响应写数据，且也允许以数据块读文件 --> `ReadStream`  

- 高效内存分配
- 数据一旦就绪立即可写入

### ReadStream
当对于大文件如图片分块传输，会使用对文件创建流对象`createReadStream`  

```
<script>
    require('http').createServer(function(req, res){
        res.writeHead(200, {
            'Content-Type': 'image/png'
        });
        var stream = require('fs').createReadStream('xxx.png');
        stream.on('data', function(data){
            // 对接口
            res.write(data);
        });
        stream.on('end', function(){
            // 对接口
            res.end();
        });
    }).listen(8080);
</script>

```

其中，其实是在实现**流的对接**  (`.pipe()`)  ，即流(Stream)文件系统接上了另一流(http.ServerResponse对象)，所以，上述代码等价于  

```
<script>
    require('http').createServer(function(req, res){
        res.writeHead(200, {
            'Content-Type': 'image/png'
        });
        require('fs').createReadStream('xxx.png').pipe(res);
    }).listen(8080);
</script>
```

## http.request()
`http.request(option, callback)`  作为客户端想http服务器发起请求，返回一个`http.ClientRequest`  的实例  

- 参数一option为关联数组对象，表示请求参数  
| host: 请求网站域名或IP  
| port: 网站端口 默认80  
| method: 请求方法 默认GET  
| path: 请求相对根的路径 默认"/"  一般由`querystring`模块解析字符串路径为url格式编码  
| headers: 关联数组对象 请求头内容

- 参数二callback请求回调函数  
callback回调传递的参数为`http.ClientResponse`的实例

```
<script>
    // 返回http.ClientRequest的实例
    http.request({
        host: '127.0.0.1',
        port: 8080,
        url: '/',
        method: 'GET'
    }, function(res){
        // http.ClientResponse的实例
        var body = '';
        res.setEncoding('utf8');
        res.on('data', function(chunk){
            // 监听响应数据的变化
            // 以数据块形式
            // 存储至body并保存为字符串
            body += chunk;
        });
        res.on('end', function(){
            console.log('\n We got: \033[96m' + body + '\033[39m\n');
        });
    }).end(); // 结束响应
</script>

```

### http.get()
类似与`http.request()`，提供一样的功能，但用法更简洁  

```
<script>
    http.get({
        // 更加明确是GET请求
        host: '127.0.0.1',
        port: 8080,
        url: '/',
        method: 'GET'
    }, function(res){
        var body = '';
        res.setEncoding('utf8');
        res.on('data', function(chunk){
            body += chunk;
        });
        res.on('end', function(){
            console.log('\n We got: \033[96m' + body + '\033[39m\n');
        });
    }); // 使用.get() 此处省略了.end()
</script>
```

## superagent
类似于`http.request()`，一个更加便捷的客户端请求代理模块  

- get请求

使用get请求传递查询字符串，可用`.query()`，传递一个对象或字符串  

`.set()`  设置*请求头*信息  

```
<script>
    var request = require('superagent');
    request
        .get('/search')
        .query({
            scope: 103,
            format: 'json',
            appid: '379020',
            bk_key: '火影'
        })
        // 等价于
        // .query('scope=103&format=json&appid=379020&bk_key=火影')
        .set('Accept', 'application/json')
        .end(function(res){
            console.log(res.body);
        });
</script>
```

- post请求

```
<script>
    // ...
    request
        .post('/user')
        .set('Content-Type', 'application/json')
        .send({
            name: 'leon',
            job: 'engineer'
        })
        .end(callback);
</script>
```

类似于get请求的写法，当想提交数据以 `application/x-www-form-urlencoded`  类型的话，则可直接调用 `.type()`  传递form参数即可  

```
<script>
    // ...
    request
        .type('form')
        .send({
            name: 'leon'
        })
        .send({
            job: 'engineer'
        })
        .end(callback);
</script>
```






