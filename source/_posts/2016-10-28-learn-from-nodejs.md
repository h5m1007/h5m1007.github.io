---
layout: post
title: "NodeJs 全记录五"
date: 2016-10-28 16:00
categories: node javascript
tags: node nodejs 
---



前端程序猿的第二春 使用老本行JS来构建服务器端的神器  NodeJs




## Connect
一个基于HTTP服务器的工具集，提供一种新的组织代码方式来与请求、响应对象进行交互，这种方式被称作中间件  

### static中间件

- 挂载
允许将任意url匹配到文件系统中任意目录  

假设让 `/my-images` 这样的url和目录名为 `/images` 对应起来  

```
<script>
    var connect = require('connect'),
        server = connect.createServer();
    server.use(
        '/my-images',
        connect.static('/images')
    );
    server.listen(8080);
</script>
```

- maxAge
作为static的选项，实现限定资源在客户端的缓存时间  

假设，客户端所有js合并至一个文件，在其文件名加上修订号，此时，设置选项maxAge可实现永缓存起来  

```
<script>
    // ...
    server.use(
        '/js',
        connect.static(
            '/path/to/bundles',
            { maxAge: 100000000}
        );
    );
</script>
```

### query中间件
假设，客户端发送查询请求，url为 `/blog-posts?page=5` ，访问该url时，node会以字符串的形式将url存储至req.url中  

```
<script>
    // ...
    server.use(connect.query);
    server.use(function(req){
        return req.url == "/blog-posts?page=5"; // true
        return req.query.page == "5"; // true
    });
</script>
```

### logger中间件
用于对web应用诊断的工具，它将请求和响应信息打印在终端  

提供了四种日记格式：default、dev、short、tiny  

- connect.logger('dev')

```
<script>
    // ...
    server.createServer(
        connect.logger('dev'),
        function(req, res){
            res.writeHead(200);
            res.end('Hello world');
        }
    ).listen(8080);
</script>
```

等价于初始化connect服务器时调用use两次  

```
<script>
    // ...
    server.use(connect.logger('dev'));
    server.use(function(req, res, next){
        res.writeHead(200);
        res.end('Fast!');
    });
</script>
```

终端输出  

```
GET / 200 0ms
GET /favicon.ico 200 2ms
```

- 自定义格式

记录**请求**方法和IP  

```
<script>
    // ...
    server.use(
        connect.logger(
            ':method :remote-addr'
        )
    );
</script>
```

记录**响应**的content-length和content-type信息  

```
<script>
    // ...
    server.use(
        connect.logger(
            'type is :res[content-type], ' +
            'length is :res[content-length] ' +
            'and it took :response-time ms.'
        )
    );
</script>
```

### bodyparser中间件
用来解析**客户端请求body中的内容**的中间件  

```
<html>
    <form action="/" method="POST" enctype="multipart/form-data">
        <input type="file" name="doc">
        <button>Send file!</button>
    </form>
</html>

<script>
    // ...
    // 简写使用connect
    // 以下代码同时实现
    // 对connect.createServer()的使用
    // 和执行了4次的connect.createServer().use()
    server = connect(
        connect.static(__dirname), // 挂载当前执行js的目录
        connect.bodyParser(), // 使用解析中间件
        connect.logger('dev'),
        function(req, res, next){
            if('POST' == req.method){
             // 返回关联数组对象
             // 包含键为表单name值doc
             // 键doc值为上传文件的所有信息
             console.log(req.files);

             // 返回对应表单name值为doc
             // 即上传文件的所有信息
             console.log(req.files.doc);
            } else {
             next();
            }
        }
    ).listen(8080);
</script>
```

### cookieParser中间件
便捷获取cookie数据，解析Cookies头可经  `req.cookies` 得到cookies  

```
Cookie: info1=val1; info2=val2

<script>
    server.use(connect.cookieParser());
    server.use(function(req, res, next){
        // req.cookies 得到存储cookie的对象
        return req.cookies.info1 == 'val1'; // true
    });
</script>
```

### session中间件
会话中间件，多个请求间共享*用户会话*，类似于用户登录会有某种形式的会话系统，通过设置cookie来保存用户信息  

`session(option)`  
接受参数option:  

| 1. key: Cookies名，默认值为connect.sid  
| 2. store: session存储实例  
| 3. secret: session的cookie加密  
| 4. cookie: session的cookie配置

```
<script>
    var connect = require('connect'),
        users = require('./users');
    var server = connect(
        connect.logger('dev'),
        connect.bodyParser(),
        // 需要使用cookieParser中间件
        // 并且需放在session中间件前面
        // session的数据经加密后会保存到cookies中
        connect.cookieParser(),
        // session默认存储方式存储到内存
        // 服务器关闭即进程关闭
        // session会丢失
        connect.session({
            secret: 'my app secret'
        }),
        function(req, res, next){
            // 登录后显示的内容
            if('/' == req.url && req.session.logged_in){
                // req.session.logged_in
                // 判断登陆与否
                res.writeHead(200, {
                    "Content-Type": "text/html"
                });
                res.end(
                    'Welcome back, <b>' + req.session.name + '</b>.' +
                    '<a href="/logout">Logout</a>'
                );
            } else {
                next();
            }
        },
        function(req, res, next){
            // 登录页面内容
            if('/' == req.url && 'GET' == req.method){
                res.writeHead(200, {
                    'Content-Type': 'text/html'
                });
                res.end([
                    '<form action="/login" method="POST">',
                    '<fieldset>',
                    '<lengend>Please log in</lengend>',
                    '<p>User: <input type="text" name="user" /></p>',
                    '<p>Password: <input type="text" name="password" /></p>',
                    '<button>Submit</button>',
                    '</fieldset>',
                    '</form>'
                ].join(''));
            } else {
                next();
            }
        },
        function(req, res, next){
            if('/login' == req.url && 'POST' == req.method){
                res.writeHead(200);
                if(!users[req.body.user] || req.body.password != users[req.body.user].password){
                    res.end('Bad username/password');
                } else {
                    req.session.logged_in = true;
                    req.session.name = users[req.body.user].name;
                    res.end('Authenticated!');
                    console.log(JSON.stringify(req.session));
                    console.log(JSON.stringify(req.cookies));
                }
            } else {
                next();
            }
        },
        function(req, res, next){
            if('/logout' == req.url){
                req.session.logged_in = false;
                res.writeHead(200);
                res.end('Logged out!');
            } else {
                next();
            }
        }
    );
    server.listen(8080);
</script>
```

### basicAuth中间件
向用户提供基本的身份验证  

`connect.basicAuth(function(user,pass,fn))`  
提供user和pass直接指向用户输入的帐号和密码，fn回调来返回验证成功与否  
当没有回调fn，将是同步验证，反之有fn回调则为异步验证  

```
<script>
    // ...
    connect(
        connect.basicAuth(function(user, pass, fn){
            // 实现客户端基本身份异步验证
            process.stdout.write('Allow user \033[96m' +
                user +
                '\033[39m ' +
                'with pass \033[90m' +
                pass +
                '\03339m ? [y/n]: ');
            process.stdin.once('data', function(data){
                // .once 每个请求获取一次数据
                if(data[0] == 'y'){
                    fn(null, {
                        username: user // 用来生成req.remoteUser对象
                    });
                } else {
                    fn(new Error('Unauthorized'));
                }
            });
        }),
        function(req, res){
            res.writeHead(200);
            res.end('Welcome to the protected area.' +
                //req.remoteUser返回{ username: 'xxx'}
                req.remoteUser.username);
        }
    ).listen(8080);
</script>
```

