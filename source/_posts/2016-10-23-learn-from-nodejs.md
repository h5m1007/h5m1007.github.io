---
layout: post
title: "NodeJs 全记录三"
date: 2016-10-23 22:42
categories: node javascript
tags: node nodejs 
---



前端程序猿的第二春 使用老本行JS来构建服务器端的神器  NodeJs




## 用法
讨论前，引入几个关键词：  

- 全局对象  global、process
- 模块系统  require、module、exports
- 事件  EventEmitter(事件分发器)
- buffer  对二进制数据处理，对数据编码转换


## 全局对象
- global  类似window对象，global对象上任何属性都可被全局访问
- process  全局执行上下文都在process对象中，类似的如window.title表示浏览器窗口名，而process.title表示node中进程名


## 模块系统

[参考全记录一 模块](../2016/10/19/learn-from-nodejs/#section)


## 加载方式
选择一种代码组织策略，保证每个模块都是独立的，则就得确定好各模块的加载方式      

- 第一种加载方式
将模块的函数**挂载**到exports对象的**属性**上  
通过调用exports对象的属性可以获取多个该函数的实例  

**server.js**

```
<script>
    var Person = require('./person').Person,
        person1 = new Person(),
        person2 = new Person();
    person1.setName("leon1");
    person2.setName("leon2");
    person1.sayHello(); // Hello leon1
    person2.sayHello(); // Hello leon2
</script>
```

**person.js**

```
<script>
    function Person(){
      var name;
      this.setName = function(theName){
        name = theName;
      }
      this.sayHello = function(){
        console.log('Hello',name);
      }
    }
    exports.Person = Person;
</script>
```

- 第二种加载方式
第三种方式其实是对第二种方式的优化，主要利用**全局变量module**  

**server.js**

```
<script>
    var Person = require('./person'),
    // ...
</script>
```

**person.js**

```
<script>
    // ...
    module.exports = Person;
</script>
```


## 事件
node有关事件处理的API接口，EventEmitter  
该API定义了on、emit、removeListener方法  

- 应用

```
<script>
    var EventEmitter = require('events').EventEmitter,
     a = new EventEmitter;
    a.on('event', function(){
     // 为事件event注册侦听器
     console.log('event called');
    });
    a.emit('event'); // 手动触发事件event
</script>
```

- 以`process.EventEmitter`暴露接口

```
<script>
    // 添加至自建类上
    var EventEmitter = process.EventEmitter,
        MyClass = function(){}; // 声明个函数模拟类
    // __proto__让MyClass的Func继承EventEmitter所有方法和属性
    MyClass.prototype.__proto__ = EventEmitter.prototype;
    var a = new MyClass;
    a.on('event', function(){
        // 为事件event注册侦听器
        console.log('event called');
    });
    a.emit('event'); // 手动触发事件event
</script>
```


## Stream流
如同html文本流，stream流在process全局对象中包含有三流对象  

- stdin  输入流即可读流 默认状态暂停  
    `stdin.resume()`  保持用户输入状态  
    `stdin.pause()`  暂停用户输入状态  
    `stdin.on('data', option)`  用于监听输入数据变化  
- stdout  输出流即可写流  
    如 `console.log()` 输出指定字符串且其后加上(\n) 并写入stdout流  
    而 `process.stdout.write(str)` 直接输出字符串且不过行 同一行继续输出  
- stderr  错误流即可写流


## fs模块
`var fs = require('fs')` 引入node内置模块fs  

- `fs.readdir('src', function(err, files){})`  
    fs.readdir读取指定资源目录(参数一)  
    files(参数二)包含指定目录下所有**文件名称**的数组  

- `fs.stat('src', function(err, stat){})`  
    fs.stat返回文件或目录的**元数据**(描述数据的信息，即数据的属性等特性)  
    当src为 `__dirname` 时，则获取当前执行文件所在的目录  
    当src为 `process.cwd()` 时，则获取当前执行文件的工作目录即运行时的目录  
    `stat.isDirectory()`  可判断stat对象是否为目录

- `fs.readFile('src', function(err, data){})`  
    `data`  返回指定资源目录文件的内容

- `fs.createReadStream()`  为文件创建可读的Stream对象  
    对于处理大文件，采取分割文件，单块读取更高效率  

```
<script>
    var stream = fs.createReadStream('file');
    stream.on('data', function(chunk){
        // 处理单块文件内容
    });
    stream.on('end', function(chunk){
        // 文件读取完毕
    });
</script>    
```

- `fs.watchFile`监视指定文件  `fs.watch`监视整个目录  
    监视文件或目录发生变化时，分发一个事件，然后触发回调  

```
<script>
    var stream = fs.createReadStream('file'),
        fs = require('fs'),
        files = fs.readdirSync(process.cwd());

    files.forEach(function(file){
        // 监听.css后缀文件
        if(/\.css/.test(file)){
            fs.watchFile(process.cwd() + '/' + file, function(){
                console.log(' - ' + file + ' changed!');
            });
        }
    });
</script>
```


## process
访问全局执行文件上下文

- `process.stdout.write(str)`  输出字符串同一行继续输出
- `process.cwd()`  返回当前文件运行工作时所在路径
- `process.exit(1)`  退出进程
- `process.on('SIGKILL', function(){});`  接收进程终止信号