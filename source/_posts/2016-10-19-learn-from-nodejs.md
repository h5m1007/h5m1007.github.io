---
layout: post
title: "NodeJs 全记录一"
date: 2016-10-19 22:42
categories: node javascript
tags: node nodejs npm
---



前端程序猿的第二春 使用老本行JS来构建服务器端的神器  NodeJs




## 什么是NodeJs
js运行需要个解析器，写在html文档的js，运行在浏览器上，从而浏览器就充当了解析器的角色，那么需要**独立运行**的js，NodeJs就提供了这样一个平台(解析器)  

运行于浏览器的js，主要操作DOM，浏览器提供了document之类的内置对象，而运行于NodeJs的js则是可以搭建HTTP服务器甚至来操作磁盘文件，相应地提供了`fs`、`http`等内置对象  


## 模块
这里直接开始要点，前提是你已经配置安装好NodeJs，并且成功运行  

- require  
要分清这里require不是RequireJs  
RequireJs的模块加载是异步，且模块均采用AMD规范写法  
而NodeJs的require模块，模块加载主要以同步为主，并没浏览器端异步加载的需求，且模块均采用CMD规范写法  

ok，进入正题  

```
<script>
    // 两者等价 可省略后缀.js 路径可绝对或相对
    // foo1与foo2保存的是同一个模块导出对象
    var foo1 = require('./foo');
    var foo2 = require('./home/foo.js');

    // 可加载JSON文件
    var data = require('./data.json');
</script>
```

require是可加载或使用别的模块，传入模块名后返回模块导出对象  

require缓存了  `module.exports`  的值，并在以后再次调用require时，返回相同的值  

- module.exports & exports  

两者都为当前模块的导出对象，供其它模块require引入  

但  `module.exports`  是真正的接口，`exports`只是它的一个辅助工具，最后返回给调用的依旧是  `module.exports`  

两者同时存在于模块时，优先使用  `module.exports`  收集的信息  

当想模块返回特定类型时，可使用  `module.exports`  ，而返回典型的实例化对象时，使用  `exports`

当模块想导出一个函数时，函数赋值给  `exports`    将会为  `exports`  引用重新赋值，而赋值给  `module.exports`  依旧指向原始的空对象  

- module  
占位...

### 应用

```
<script>
    // count.js
    var i = 0;
    function count(){
        return ++i;
    }
    exports.count = count;
</script>

<script>
    // main.js
    var count1 = require('./count');
    var count2 = require('./count');

    console.log(count1.count()); // 输出1
    console.log(count2.count()); // 输出2
</script>
```

由命令 `node main.js` 加载主模块main，可发现输出的值，虽require加载同一模块，但test.js并没有初始化两次  


## 包
某些复杂的模块往往由多个子模块组成，一般称这些复杂模块为包  
包相当于一个文件夹，这文件夹里包含多个子模块和唯一一个入口模块，入口模块导出对象为包的导出对象，由其它模块require引入  

```
- /modules
    - module1/
        count.js
        main.js
        <!-- index.js -->
    - module2/
        main.js
```

有包module1和module2，module1有两模块，子模块count.js，主模块main.js  

`var module1 = require(../module1/main)`  在module2的主模块中这样引入module1的包  

`var module1 = require(../module1)`  当module1的主模块命名为index.js时，module2可这样引入包  

- 自定义入口模块名称与位置  
想为module1自定义主模块名称和存放位置，可在module1包里新建个package.json文件，；来指定其主模块路径与模块名  

```
<script>
    // package.json
    {
        "name": "module1",
        "main": "../module1/main.js"
    }
</script>
```

然后依旧能在module2中这样引入module1包，  `var module1 = require(../module1)`  

## package.json
为项目创建package.json，利于在本地测试，方便发布版本  

```
{
    "name": "my-colors-project",
    "version": "0.0.1",
    "main": "./index",
    "dependencies": {
        // 声明依赖
        "colors": "0.5.0" // 锁定依赖模块版本
    }
}
```

当运行主模块时，首先会检测package.json(有的话)，运行指定的主模块路径，并下载自指定依赖模块(没的话)，根据依赖模块版本，防止下载最新版本会导致程序出错  

## 快速塔建node本地服务器

```
<script>
    var http = require('http'),
        sev = http.createServer(function(req, res){
            /*
                req用来接受客户端数据
                res用来向客户端发送服务器数据
            */
            res.writeHead(
                200,
                {'Content-Type': 'text/html'}
            );
            res.end('<marquee>Smashing Node!</marquee>');
        }).listen(8080);
    console.log("服务器启动成功");
</script>
```

