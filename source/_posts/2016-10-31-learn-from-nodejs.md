---
layout: post
title: "NodeJs 全记录六"
date: 2016-10-31 14:03
categories: node javascript
tags: node nodejs express
---



前端程序猿的第二春 使用老本行JS来构建服务器端的神器  NodeJs




## express
作为node框架express，基于connect构建而成，保持重用中间件的做法，更加便捷构建web应用  

### 模版引擎

模版语言，用来分离逻辑代码和外在内容，使前后台设计时，对页面或逻辑改动时，相互不影响，更加分清前端和后台设计的工作  

### 应用

- 假设已安装模块 `npm install ejs` 和 `npm install superagent`  

```
<!-- server.js -->
<script>
    var express = require('express'),
        search = require('./search'),
        app = express.createServer();
    app
        .set('view engine', 'ejs') // 指明模版引擎
        .set('views', __dirname + '/views') // 指明模版根目录
        .set('view options', {
            // 参数值传递每个模版
            layout: false
        })
        .set('view cache', true); // 对模版缓存
    /**
     * 配置路由处理器
     */
    app.get('/', function(req, res){
        // 渲染index模版
        // 并作为响应返回
        res.render('index');
        // console.log(app.set('views'));
    });
    app.get('/search', function(req, res, next){
        search(req.query.q, function(err, baikes){
            // req.query 获取地址栏传递的参数
            if (err) {
                // 调用search(qstr, fn)函数
                // 经fn实例化的错误中间件
                return next(err);
            }
            res.render(
                'search',
                {
                    // 传递变量到search.ejs视图
                    // 这类变量称作本地变量
                    // 因只对其传递视图可见
                    results: baikes,
                    search: req.query.q
                }
            );
        });
    });
    /**
     * 配置错误处理器
     */
    app.error(function(err, req, res, next){
        // 配置search(qstr, fn)函数
        // fn实例化的错误中间件参数
        if('Bad baidu baike response' == err.message){
            res.render(
                'error',
                {
                    status: 500
                },
                function(err, html){
                    // 第三参数
                    // 可处理收到的html
                }
            );
        } else {
            next();
        }
    });
    app.listen(8080);
</script>
```
`express.createServer` 返回HTTP服务器自带配置系统  

调用对象 `app` 上的 `set()` 来修改默认配置项  

经express框架处理路由，省去了对 `req.url` 和 `req.method` 的判断  

express为response对象提供了 `render()` 渲染模版方法，渲染过程：  

| 初始化模版引擎  

| 读取视图文件并将其传递给模版引擎  

| 获取解析后的HTML页面并作为响应发送给客户端  


- 结合使用模版语言 可选Ejs或jade  

```
<!-- index.ejs -->
<h1>Baike Search App</h1>
<p>Please enter your search term:</p>
<form action="/search" method="GET">
    <input type="text" name="q">
    <button>Search</button>
</form>
```

表单里，提交到 `/search` ，所带参数有 `q=` input标签输入的内容，最终以 `search?q=xxx` 跳转到提交页面，而地址栏参数，可由 `req.query.q` 获取  

```
<!-- search.ejs -->
<h1>Baidu Baike Search results for <%= search %></h1>
<% if (results.length) { %>
    <ul>
        <% for (var i = 0; i < results.length; i++) { %>
        <li><%= results[i].name %> - <em><%= results[i].value %></em></li>
        <% } %>
    </ul>
<% } else { %>
    <p>No resultes</p>
<% } %>
```

ejs模版语言，js代码嵌入`<%`和`%>`间，在`<%`后加入`=`符号将变量打印出来  

### 扩展
express为node的Request和Response对象提供了一系列扩展来简化开发  

- `req.header('host')`  直接获取头信息
- `req.accepts('html')`  判断Accept头信息，返回布尔值
- `req.is('text/html')`  判断 `Content-Type` 头信息

- `res.header('content-type')`  判断指定值是否已在res上设置
- `res.send(500)`  直接发送状态码
- `res.send('<p>html</p>')`  直接发送HTML
- `res.send({ obj: val })`  序列化对象为JSON对象，并设置 `Content-Type` 头信息
- `res.send([1, 2])`  序列化数组为JSON对象，并设置 `Content-Type` 头信息
- `res.json(5)`  显示地将内容转成JSON对象
- `res.redirect('/some/other/url')`  等价于发送状态码302及Location头信息  
- `res.sendfile('image.jpg')`  ；类似于`static`，不同于它用于单个文件  

### 路由
`app.get('/', callback)`

- 自定义参数

```
<script>
    // ...
    app.get('/user/:name', function(req, res, next){
        return req.params.name == 'leon'; // true
    })
</script>
```  

name变量会注入 `req.params` 请求参数对象上，匹配url如 `/user/leon`，而不会匹配到 `/user/`  

`app.get('/user/:name?')` 当name变量后添加"?"时，将可以匹配到如 `/user` 和 `/user/leon`  

`app.get(/^\/user/([a-z\d\-]*)/)` 可使用正则匹配，将匹配字母、数字及中划线  

- 强制匹配其它路由
当一个路由匹配到并得到处理后，同时可以强制继续匹配其它路由  

```
<script>
    app.get('/user/:name', function(req, res, next){
        if('leon' != req.params.name[0]) return next();
    });
</script>
```

- 中间件
与connect一样地使用中间件，如 `express.static(__dirname)` `express.session()`  

同时，express允许在*特定匹配到的路由*下才能使用的中间件  

```
<script>
    function secure(req, res, next){
        if(!req.session.logged_in){
            return res.send(403); //未授权
            
            // 跳过路由的其它中间件
            // 直接到下一个路由做处理
            // return next('route'); 
        }
    }
    app.get(
        '/finacials',
        secure,
        function(){}
    )
</script>
```

## express的加载方式
选择一种代码组织策略，保证每个模块都是独立的，则就得确定好各模块的加载方式  

- 第一种加载方式
良好的代码组织方式应只维护一个 `server.js` 文件，而该文件包含有路由表，且每一部分路由处理器都通过模块化的方式引入  

**server.js**

```
<script>
    var express = require('express'),
        pages = require('./pages'),
        app = express.createServer();
    app.get('/pages', pages.home);
    app.get('/pages/list', pages.list);
</script>
```

**pages.js**

```
<script>
    exports.home = function(req, res, next){}
    exports.list = function(req, res, next){}
</script>
```

而第一种加载方式，会*出现的问题*是，每个模块引入时，都是指向同一个实例对象 `exports` ，也就是说 `require('./pages')` 该模块只能使用一次，因require只会加载一次该模块，不管后面有多少次使用了 `require('./pages')`    

- 第二种加载方式
可以看出，第一种加载方式模块间的**耦合度高**，而第二种加载方式通过一种解耦方式**app挂载**来降低模块间耦合度  

**server.js**

```
<script>
    // ...
    app.use(require('./pages'));
</script>
```

**pages.js**

```
<script>
    var app = module.exports = express.createServer();
    app.get('/', function(req, res, next){});
    app.get('/list', function(req, res, next){});
</script>
```

把每个模块单独作为中间件让其它模块使用，这样模块本身就是完全独立的依赖、中间件、配置，等等  



